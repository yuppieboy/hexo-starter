---
title: iOS 9 全新Contacts Framework解析
date: 2016-07-18 09:17:20
categories: iOS
tags: contact

---


## 引言 ##
在以前iOS开发中，涉及联系人相关的编程，代码都非常繁琐，并且框架的设计也不是Objective-C风格的，这使开发者用起来非常的难受。在iOS9中，apple终于解决了这个问题，全新的Contacts Framework将完全替代AddressBookFramework，AddressBookFramework也将成为历史被弃用。

## Contact框架的使用 ##
 >CNContact基本属性

    /**
     *  设置联系人的基本属性
     *
     *  @return 返回联系人的对象
     */
    - (CNMutableContact *)initializeContact{
        // 创建联系人对象
        CNMutableContact *contact = [[CNMutableContact alloc] init];
        // 设置联系人的头像
        contact.imageData = UIImagePNGRepresentation([UIImage imageNamed:@"7677360.png"]);
        // 设置联系人姓名
        contact.givenName = @"伟君";
        // 设置姓氏
        contact.familyName = @"丁";
        // 设置联系人邮箱
        CNLabeledValue *homeEmail = [CNLabeledValue labeledValueWithLabel:CNLabelHome value:@"12345qq.com"];
        CNLabeledValue *workEmail = [CNLabeledValue labeledValueWithLabel:CNLabelWork value:@"32467@sina.cn"];
        CNLabeledValue *otherEmail = [CNLabeledValue labeledValueWithLabel:CNLabelOther value:@"lanaj.com"];
        contact.emailAddresses = @[homeEmail,workEmail,otherEmail];
        // 设置机构名
        contact.organizationName = @"互联网";
        // 设置部门
        contact.departmentName = @"IT";
        // 设置工作的名称
        contact.jobTitle = @"ios";
        // 设置社会的简述
        CNSocialProfile *profile = [[CNSocialProfile alloc] initWithUrlString:@"12306.cn" username:@"Paul" userIdentifier:nil service:@"IT行业"];
        CNLabeledValue *socialProfile = [CNLabeledValue labeledValueWithLabel:CNSocialProfileServiceGameCenter value:profile];
        contact.socialProfiles = @[socialProfile];
        // 设置电话号码
        CNPhoneNumber *mobileNumber = [[CNPhoneNumber alloc] initWithStringValue:@"18500000000"];
        CNLabeledValue *mobilePhone = [[CNLabeledValue alloc] initWithLabel:CNLabelPhoneNumberMobile value:mobileNumber];
        contact.phoneNumbers = @[mobilePhone];
        // 设置与联系人的关系
        CNContactRelation *friend = [[CNContactRelation alloc] initWithName:@"好基友"];
        CNLabeledValue *relation = [CNLabeledValue labeledValueWithLabel:CNLabelContactRelationFriend value:friend];
        contact.contactRelations = @[relation];
        // 设置生日
        NSDateComponents *birthday = [[NSDateComponents alloc] init];
        birthday.day = 6;
        birthday.month = 5;
        birthday.year = 2000;
        contact.birthday = birthday;

        return contact;
    }



>CNSaveRequest使用


    @interface CNSaveRequest : NSObject
    //添加一个联系人
    - (void)addContact:(CNMutableContact *)contact toContainerWithIdentifier:(nullable NSString *)identifier;

    //更新一个联系人
    - (void)updateContact:(CNMutableContact *)contact;
    //删除一个联系人
    - (void)deleteContact:(CNMutableContact *)contact;
    //添加一组联系人
    - (void)addGroup:(CNMutableGroup *)group toContainerWithIdentifier:(nullable NSString *)identifier;
    //更新一组联系人
    - (void)updateGroup:(CNMutableGroup *)group;
    //删除一组联系人
    - (void)deleteGroup:(CNMutableGroup *)group;
    //向组中添加子组
    - (void)addSubgroup:(CNGroup *)subgroup toGroup:(CNGroup *)group NS_AVAILABLE(10_11, NA);
    //在组中删除子组
    - (void)removeSubgroup:(CNGroup *)subgroup fromGroup:(CNGroup *)group NS_AVAILABLE(10_11, NA);
    //向组中添加成员
    - (void)addMember:(CNContact *)contact toGroup:(CNGroup *)group;
    //向组中移除成员
    - (void)removeMember:(CNContact *)contact fromGroup:(CNGroup *)group;

    @end

>CNSaveRequest的封装使用


    /**
     *  在group里删除成员
     *
     *  @param contact 被删除的联系人
     *  @param group   在该group里删除联系人
     */
    - (void)deleteMemberWithContact:(CNContact *)contact toGroup:(CNGroup *)group{
        CNSaveRequest *saveRequest = [[CNSaveRequest alloc] init];
        [saveRequest removeMember:contact fromGroup:group];
        // 写入
        CNContactStore *store = [[CNContactStore alloc] init];
        [store executeSaveRequest:saveRequest error:nil];

    }

    /**
     *  向group添加成员
     *
     *  @param contact 被添加的联系人
     *  @param group   添加到该group
     */
    - (void)addMemberWithContact:(CNContact *)contact toGroup:(CNGroup *)group{
        CNSaveRequest *saveRequest = [[CNSaveRequest alloc] init];
        [saveRequest addMember:contact toGroup:group];
        // 写入
        CNContactStore *store = [[CNContactStore alloc] init];
        [store executeSaveRequest:saveRequest error:nil];

    }

    /**
     *  更新group
     *
     *  @param group 被更新的group
     */
    - (void)updateGroup:(CNMutableGroup *)group{
        CNSaveRequest *saveRequest = [[CNSaveRequest alloc] init];
        [saveRequest updateGroup:group];
        // 写入
        CNContactStore *store = [[CNContactStore alloc] init];
        [store executeSaveRequest:saveRequest error:nil];
    }

    /**
     *  删除group操作
     *
     *  @param group 被删除的group
     */
    - (void)deleteWithGroup:(CNMutableGroup *)group{
        CNSaveRequest *saveRequest = [[CNSaveRequest alloc] init];
        [saveRequest deleteGroup:group];
        // 写入
        CNContactStore *store = [[CNContactStore alloc] init];
        [store executeSaveRequest:saveRequest error:nil];
    }

    /**
     *  查询
     *
     *  @return 返回数组
     */
    - (NSArray *)queryGroup{
        CNContactStore *store = [[CNContactStore alloc] init];
        // 查询所有的group(predicate参数为空时会查询所有的group)
        NSArray *arr = [store groupsMatchingPredicate:nil error:nil];
        return arr;
    }

    /**
     *  添加群组
     *
     *  @param name 群组的名称
     */
    - (void)addGroupWithName:(NSString *)name{
        CNMutableGroup *group = [[CNMutableGroup alloc] init];
        group.name = name;
        CNSaveRequest *saveRequest = [[CNSaveRequest alloc] init];
        [saveRequest addGroup:group toContainerWithIdentifier:nil];
        // 写入
        CNContactStore *store = [[CNContactStore alloc] init];
        [store executeSaveRequest:saveRequest error:nil];
    }

    /**
     *  查询操作
     *
     *  @return 返回数组
     */
    - (NSArray *)queryContactWithName:(NSString *)name{
        CNContactStore *store = [[CNContactStore alloc] init];
        // 检索条件
        NSPredicate *predicate = [CNContact predicateForContactsMatchingName:name];
        // 提取数据 （keysToFetch:@[CNContactGivenNameKey]是设置提取联系人的哪些数据）
        NSArray *contact = [store unifiedContactsMatchingPredicate:predicate keysToFetch:@[CNContactGivenNameKey] error:nil];
        return contact;
    }

    /**
     *  更新联系人
     *
     *  @param contact 被更新的联系人
     */
    - (void)updateContact:(CNMutableContact *)contact{
    // 创建联系人请求
        CNSaveRequest *saveRequest = [[CNSaveRequest alloc] init];
        [saveRequest updateContact:contact];
        // 重新写入
        CNContactStore *store = [[CNContactStore alloc] init];
        [store executeSaveRequest:saveRequest error:nil];

    }

    /**
     *  删除联系人
     *
     *  @param contact 被删除的联系人
     */
    - (void)deleteContact:(CNMutableContact *)contact{
        // 创建联系人请求
        CNSaveRequest *saveRequest = [[CNSaveRequest alloc] init];
        [saveRequest deleteContact:contact];
        // 写入操作
        CNContactStore *store = [[CNContactStore alloc] init];
        [store executeSaveRequest:saveRequest error:nil];

    }

    /**
     *  添加联系人
     *
     *  @param contact 联系人
     */
    - (void)addContact:(CNMutableContact *)contact{
        // 创建联系人请求
        CNSaveRequest *saveRequest = [[CNSaveRequest alloc] init];
        [saveRequest addContact:contact toContainerWithIdentifier:nil];
        // 写入联系人
        CNContactStore *store = [[CNContactStore alloc] init];
        [store executeSaveRequest:saveRequest error:nil];
    }


>实际工程中的操作使用

    - (void)viewDidLoad {
        [super viewDidLoad];
        // Do any additional setup after loading the view, typically from a ni
        CNMutableContact *contact = [self initializeContact];    
        //添加联系人
        [self addContact:contact];
        //查询
        NSArray *arr = [self queryContactWithName:@"伟君"];
        //将不可变的CNContact转成CNMutableContact，便可执行以下操作
        CNMutableContact *contact = [arr[0] mutableCopy];
        //更新
        contact.givenName = @"大力";
        [self updateContact:contact];
        //删除
        [self deleteContact:contact];

        //添加群组
        [self addGroupWithName:@"Yudo"];
        //返回所有数组
        NSArray *array = [self queryGroup];
        NSLog(@"==%@",array);
        //删除group操作
        CNMutableGroup *group = [array[2] mutableCopy];
        [self deleteWithGroup:group];

        //更新group
        CNMutableGroup *group = [array[2] mutableCopy];
        group.name = @"Yudo";
        [self updateGroup:group];

        //向group添加成员
        NSArray *results = [self queryContactWithName:@"伟君"];
        for (int i=0; i<array.count; i++) {
            CNGroup *group=array[i];
            if ([group.name isEqualToString:@"Yudo"]) {
                [self addMemberWithContact:results[0] toGroup:group];
            }
        }


        //在group里删除成员
        NSArray *results = [self queryContactWithName:@"baba zhang"];
        for (int i=0; i<array.count; i++) {
            CNGroup *group=array[i];
            if ([group.name isEqualToString:@"Yudo"]) {
                [self deleteMemberWithContact:results[0] toGroup:group];
            }
        }
    }
