## 数据集

#### knowledge-point

* knowledge_point_id: 知识点id
* knowledge_point_name: 知识点名
* knowledge_point_parent-id: 引用 knowledge_point_id 表明该知识点的父节点，如果该字段为空，说明该知识点是 root

#### problem

* problem_id: 题目id
* user_id: 出题人id
* organization_id: 单位id
* content: 题面
* difficulty: 教师自定的难度
* problem_config: 非编程题此字段无意义，编程题会包含测试点的 metadata，json格式，如下例子中该题有三个测试点

```
{
    "programmingProblemConfig":{
        "cases":{
            "0":{
                "hint":"同sample",
                "score":9,
                "updateAt":"2015-07-05T21:01:33.000Z"
            },
            "1":{
                "hint":"最小N",
                "score":2,
                "updateAt":"2015-07-05T21:01:33.000Z"
            },
            "2":{
                "hint":"较大N",
                "score":4,
                "updateAt":"2015-07-05T21:01:33.000Z"
            }
        },
        "codeSizeLimit":16,
        "memoryLimit":65536,
        "timeLimit":400
    }
}

// definition
message ProblemConfig {
    oneof config {
        FillInTheBlankForProgrammingProblemConfig fill_in_the_blank_for_programming_problem_config = 1;
        CodeCompletionProblemConfig code_completion_problem_config = 2;
        ProgrammingProblemConfig programming_problem_config = 3;
        MultipleChoiceMoreThanOneAnswerProblemConfig multiple_choice_more_than_one_answer_problem_config = 4;
        FillInTheBlankProblemConfig fill_in_the_blank_problem_config = 5;
        SubjectiveProblemConfig subjective_problem_config = 6;
        MultipleFileProblemConfig multiple_file_problem_config = 8;
    }
    bool solution_visible = 7;
}
message FillInTheBlankProblemConfig {
    repeated int32 scores = 1;
    int32 blank_length = 2; // blank length is only used for frontend display and limitation.
}

message FillInTheBlankForProgrammingProblemConfig {
    int32 time_limit = 1;
    int32 memory_limit = 2;
    int32 blank_length = 3; // blank length is only used for frontend display and limitation.
    repeated int32 scores = 5;
    map<string, ProblemCaseConfig> cases = 6;
}

// Field cases should not be provided to student user when showing problem in problem set, but it's ok to show score
// and hint (if show_hint flag is set to true) when student requests a submission detail after submission.
message CodeCompletionProblemConfig {
    int32 time_limit = 1; // ms
    int32 memory_limit = 2; // KB
    int32 code_size_limit = 3; // KB
    map<sng, ProblemCaseConfig> cases = 5;
    repeated ProblemTestData exampleTestDatas = 6; // 1..5
    string testdata_description_code = 7;
}

message MultipleChoiceMoreThanOneAnswerProblemConfig {
    int32 options_count = 1;
}

message CompilerAndLimit {
    core.model.Compiler compiler = 1;
    int32 time_limit = 2; // ms
    int32 memory_limit = 3; // KB
}

// Field cases should not be provided to student user when showing problem in problem set, but it's ok to show score
// and hint (if show_hint flag is set to true) when student requests a submission detail after submission.
message ProgrammingProblemConfig {
    int32 time_limit = 1; // ms
    int32 memory_limit = 2; // KB
    int32 code_size_limit = 3; // KB
    map<string, ProblemCaseConfig> cases = 5; // 1..20
    repeated ProblemTestData exampleTestDatas = 6; // 1..5
    string testdata_description_code = 7;
    repeated CompilerAndLimit customize_limits = 8;
}

message SubjectiveProblemConfig {
    bool allow_file_upload = 1;
}

message MultipleFileProblemConfig {
    int64 memory_limit = 1;
    int64 cpu_count = 2;
    string disk_limit = 3;
    repeated MultipleFiles files = 4;
}

```

* reference_count: 引用次数
* score: 题目总分
* title: 标题（判断选择标题和内容一样）
* type: 题目类型

```
enum ProblemType {
    NO_PROBLEM_TYPE = 0;
    TRUE_OR_FALSE = 1;  // 判断
    MULTIPLE_CHOICE = 2;  // 单选
    MULTIPLE_CHOICE_MORE_THAN_ONE_ANSWER = 3; // 多选
    FILL_IN_THE_BLANK = 4;  // 填空
    FILL_IN_THE_BLANK_FOR_PROGRAMMING = 5;  // 程序填空
    CODE_COMPLETION = 6;  // 函数
    PROGRAMMING = 7;  // 编程
    SUBJECTIVE = 8;  // 主观
    MULTIPLE_FILE = 9;  // 多文件编译
}
```


#### problem_knowledge_point

* knowledge_point_id: 知识点id
* problem_id: 题目id

#### problem_set

* problem_set_id: 题目集id
* create_at: 开始时间
* end_at: 结束时间
* user_id: 出卷人id

#### problem_set_problem

* problem_set_problem_id: 题集内题id
* create_at: 创建时间
* problem_config: 题目配置，类似 problem 的同名字段，学生做题时使用 problem_set_problem 的配置，老师自定义题目分数相关信息就在这里
* score: 题目总分，行为类似 problem_config
* deleted: 删除标志位
* problem_id: 题目id
* problem_set_id: 题目集id

#### user_group_member

* user_group_member_id: 关联表id 应按没啥用
* user_id: 用户id
* user_group_id: 用户组id

#### user_group_problem_set

* user_group_problem_set_id: 关联表id 应该没啥用
* user_group_id: 用户组id
* problem_set_id: 题目集id

#### organzation_member

* organization_member_id: 关联表id 应该没啥用
* user_id: 用户id
* organization_id: 单位id

Note: 这里的都是教师

#### student_user

* student_user_id: 学生帐号id 应该没啥用
* user_id: 用户id
* organization_id: 单位id

Note: 这里都是学生，如果有用户既不是老师，又不是学生，那他就是一个普通用户，不从属与任何学校的做题者

#### submission

* submission_id: 提交id
* user_id: 用户id
* create_at: 提交时间
* judge_response: 评测结果，完整定义如下

```
message JudgeResult {
    bool success = 3;
    bool finished = 4;
    string cause = 5;
    repeated JudgeResponseContent contents = 6;
}
message JudgeResponseContent {
    int64 problem_set_problem_id = 8;
    SubmissionStatus status = 1;
    double score = 2;
    oneof content {
        FillInTheBlankForProgrammingResponseContent fill_in_the_blank_for_programming_response_content = 3;
        CodeCompletionJudgeResponseContent code_completion_judge_response_content = 4;
        ProgrammingJudgeResponseContent programming_judge_response_content = 5;
        FillInTheBlankJudgeResponseContent fill_in_the_blank_judge_response_content = 6;
        SubjectiveJudgeResponseContent subjective_judge_response_content = 7;
        MultipleFileJudgeResponseContent multiple_file_judge_response_content = 9;
    }
}
message FillInTheBlankJudgeResponseContent {
    message SingleContent {
        SubmissionStatus status = 1;
        double score = 2;
    }
    repeated SingleContent contents = 1;
}

message FillInTheBlankForProgrammingResponseContent {
    message SingleContent {
        SubmissionStatus status = 1;
        double score = 2;
        CompilationResult compilation_result = 3;
        CompilationResult checker_compilation_result = 4;
        TestcaseJudgeResult testcase_judge_result = 5;
    }
    repeated SingleContent contents = 1;
}

message CodeCompletionJudgeResponseContent {
    CompilationResult compilation_result = 1;
    CompilationResult checker_compilation_result = 2;
    map<string, TestcaseJudgeResult> testcase_judge_results = 3;
}

message ProgrammingJudgeResponseContent {
    CompilationResult compilation_result = 1;
    CompilationResult checker_compilation_result = 2;
    map<string, TestcaseJudgeResult> testcase_judge_results = 3;
}

message SubjectiveJudgeResponseContent {
    string comment = 1;
}

message MultipleFileJudgeResponseContent {
    string stderr = 1;
    string stdout = 2;
    string info = 3;
}
message TestcaseJudgeResult {
    SubmissionStatus result = 1;
    TestcaseJudgeResultExceed exceed = 2;
    double time = 3; // in second
    int32 memory = 4; // in byte
    int32 exitcode = 5;
    int32 termsig = 6;
    string error = 7;
    string stdout = 8;
    string stderr = 9;
    string checker_output = 10;
}

enum TestcaseJudgeResultExceed {
    UNKNOWN_TESTCASE_EXCEED = 0;
    CPU_TIME = 1;
    REAL_TIME = 2;
    MEMORY = 3;
    OUTPUT = 4;
}
```

* problem_type: 题目类型
* score: 得分
* status: 提交状态

```
enum SubmissionStatus {
    WAITING = 0;
    JUDGING = 1;
    OVERRIDDEN = 2;
    COMPILE_ERROR = 3;
    PARTIAL_ACCEPTED = 4;
    // The following status are produced by ljudge.
    ACCEPTED = 5;
    WRONG_ANSWER = 6;
    MULTIPLE_ERROR = 7;
    INTERNAL_ERROR = 8;
    TIME_LIMIT_EXCEEDED = 9;
    MEMORY_LIMIT_EXCEEDED = 10;
    OUTPUT_LIMIT_EXCEEDED = 11;
    SEGMENTATION_FAULT = 12;
    FLOAT_POINT_EXCEPTION = 13;
    PRESENTATION_ERROR = 14;
    RUNTIME_ERROR = 15;
    NON_ZERO_EXIT_CODE = 16;
    // The following status is only available in objective problems.
    NO_ANSWER = 17;
    // The following status is set immediately after rejudge operation.
    REJUDGING = 18;
    // Cases are skipped in ACM exams if encoutering unaccepted case
    SKIPPED = 19;
    SAMPLE_ERROR = 20;
}
```

* problem_set_id: 题目集id
* problem_set_problem_id: 题集内题目id （判断选择填空均为聚合提交，也就是一次提交会包含这个卷子里所有这个类型的题目，所以这些题型该值为空，对函数题编程题有效）

#### 关于脱敏

user_id 会做数据脱敏
学校这边数据也要做脱敏，现在数据里只有 organization_id, 如果要给学校打 tag 的话，这里列了一张学校名的表，需要打好 tag 之后整合数据再给你们

#### 数据内容

数据含义不清的字段，特别是 json 形式的数据结构，他们的 proto 定义都贴在这儿了，用的是 protobuf 定义数据的，转换成 json 会有一些命名上的变化，下划线式的和驼峰式的转变。枚举类型的取值也同样贴出来了。

现在 problem problem_set submission 这三个表是用数据结构的知识点做了一下筛选的，其他的如果正式导出的话应该都是全量。