# Gitlab 项目管理指南
本文主要介绍结合现有的 Gitlab 平台进行软件项目开发管理，主要为了达到以下几个目的：

- 能够更好的把控项目节奏
- 更加有效的进行bug管理
- 促进团队成员之间的协作

## 模式
围绕 `Gitlab` 平台的 `Issue` + `Milestone` 功能来进行项目需求管理、进度把控以及 `bug` 跟踪修复。


### Issue 是项目管理中的重点，主要包括以下功能：

1.  用于登记 Bug 与需求
2.  可以按照 Issue 类型不同打上不同的 Tag
3.  每个 Issue 在每个 Project 中有唯一的 Id
4.  项目负责人可以 @相应的开发 进行开发
5.  在经过单元测试 或 Code Review后，如果功能点符合，可以关闭相应的 Issue
6.  可以通过 Gitlab 的 web 界面进行相应的分析，比如按照 Tag 和 Assignee 进行筛选

### Milestones 是项目开发中的一个里程碑，主要用来把控项目的进度及节奏

1. 所有项目以周为单位建立里程碑
2. 原则上本周的里程碑不接受新的 Issue，除非是严重的 Bug 或者紧急需求，需要马上调整
3. 里程碑的名字以项目版本为标识，例如 “v1.0.0”
4. 除了本周正在进行的里程碑之外，还可以往后规划一个或者多个里程碑，例如可以创建一个 Backlog 的 里程碑，用于存放并不紧急的需求。
5. 如果 Milestone 由于 Bug 等因素导致延期，排查具体延期原因及具体延期时间，如果时间不长则延长此 Milestone 周期，如果延期时间较长则团队需要重新规划 Milestone。

## 流程

![gitlab-project-management](./gitlab-project-management.png
)

### 参与人员定义以及职责

**Product Owner：**

产品经理，以下简称 PO。其主要职责是确认需求，跟踪节点，以及整理下期需求

**Project Manager：**

项目经理，以下简称 PM。其主要职责是拆分需求，确定优先级别，并合理分配给开发人员开发。还需要对开发的节奏进行把控，创建 Milestones。 

**Developer：**

开发人员，以下简称 Dev。其主要职责完成 PM 分配的开发任务，并修复 QA 提出的 Bugs。 

**Quality Assurance：**

测试人员，以下简称 QA。其主要职责是对完成的功能进行测试，可以是白盒测试、黑盒测试、性能测试等等。

### 流程解析及主要事项：
1. (PO) 确定一个迭代需要开发的需求，并且组织开发人员进行需求评审。
2. (PM) 对需求进行拆分，创建 Feature Issue 并且确定 Issue 的优先级别。
3. (PM) 创建 Milestone，并把 Issue 加入到当前的 Milestone 里面，允许有一些不紧要的 Issue 可以放在 Backlog 里面。
4. (PM) 配分给开发者 (Dev)。
5. (Dev) 接受任务并根据 Issue 的需求进行开发。
6. (Dev) 完成开发后，关闭 Issue ，待所有 Mliestones 里面的 Issue 都已经关闭后便可以通知 QA 进行测试。
7. (QA) 在 Milestones 面板中查看已经完成的 Issue，并对涉及到功能进行测试。
8. (QA) 测试通过，则通知 PM 关闭此次的 Mliestone。
9. (QA) 测试不痛过，则新建 Bug Issue，通过 Label 添加 Bug 的优先级别，通知相关开发者进行修复。
10. (Dev) 收到通知后，修复 bug，完成后，通知测试进行再次测试，之后重复步骤8。

## 具体实施细节
### 关于 Issue 的 Label 的定义

|  类型  |  文本  |   颜色  |
| ------| ------ | ------ |
| 新功能 | Feature |   #69D100	     |
| 问题   | Bug   |    #FF0000     |
| 探索   | Discovery | 	 #428BCA  |
| 干扰   | Impediment | #34495E     |
| 过时   | Spike      | #AD8D43     |
| 紧急的功能或者 Bugs  | Important | #CC0033   |
| 一般不紧急的功能或者 Bugs |  Major| #D9534F |

### 完成的定义

