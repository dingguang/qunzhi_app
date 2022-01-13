# qunzhi
群智系统是实现人机协同方式构造高质量知识模型的重要实现方式。通过发挥群体的智慧，来解决机器算法难以解决的创造性、推理性和开放性等问题

## 群智系统后端代码情况如下所示


    1、controller
    这部分主要为接口情况
        a、CsUserController
该部分主涉及用户控制
import io.swagger.annotations.Api;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;
import springfox.documentation.annotations.ApiIgnore;

import java.util.ArrayList;

@Slf4j
@RestController
@RequestMapping(CsConst.UserApiPrefix)
@Api(tags = "群智工作者会话", value = "群智工作者会话")
public class CsUserController {

    @Autowired
    private CsUserService userService;

    @Autowired
    private CsUserInfoService userInfoService;

    @PostMapping("/echo")
    public String echo(@RequestParam String input) {
        return input;
    }

    @PostMapping("/login/check")
    public String checkLogin(@RequestParam String code, String data) {
        return new String();
        // userService.checkAndLogin(code, data);
        //        return new CSUser();
    }

    @PostMapping("/user/domain")
    public CsUser setUserDomain(@ApiIgnore @Session CSUserSession session,
                                @RequestParam ArrayList<String> domains) {
        return userInfoService.setUserDomain(session, domains);
    }

}
        b、CsUserInfoController
该部分涉及用户信息管理，主要获取用户的当前信息
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import springfox.documentation.annotations.ApiIgnore;

import java.util.List;

@Slf4j
@RestController
@RequestMapping(CsConst.UserApiPrefix)
@Api(tags = "群智工作者信息", value = "群智工作者信息")
public class CsUserInfoController {

    @Autowired
    private CsUserInfoService userInfoService;

    @GetMapping("/current")
    public CsUserDetail getCurrent(@ApiIgnore @Session CSUserSession session) {
        return userInfoService.getCurrent(session);
    }

    @GetMapping("/domain/list")
    public List<CsUserDomainView> getUserDomainList(String domain) {
        return userInfoService.getUserDomainData(domain);
    }
}
        c、ManagerController
主要获取Task和Worker的情况
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import java.util.ArrayList;

// @Slf4j
@Api("群智任务管理")
@RestController
@RequestMapping(CsConst.ManagerApiPrefix)
public class ManageController {

    @Autowired
    public ManageService manageService;

    @ApiOperation("获取所有任务")
    @GetMapping("/task/list")
    public ArrayList<CsTask> getAllTask() {
        return manageService.getAllTasks();
    }

    @ApiOperation(value = "获取任务详情")
    @GetMapping("/task/detail")
    public ManagementTask getTaskDetail(@RequestParam long taskId) {
        return manageService.getTaskDetail(taskId);
    }

    @ApiOperation(value = "根据taskId获取userTasks")
    @GetMapping("/task/userTask/list")
    public ArrayList<CsUserTask> getUserTaskByTaskId(@RequestParam long taskId) {
        return manageService.getUserTaskByTaskId(taskId);
    }

    @ApiOperation(value = "获取用户任务详情")
    @GetMapping("/task/userTask/detail")
    public CsUserTask getUserTask(@RequestParam long id) {
        return manageService.getUserTaskDetail(id);
    }

    @ApiOperation(value = "获取所有众包工作者")
    @GetMapping("/worker/list")
    public ArrayList<CsUser> getAllWorker() {
        return manageService.getAllUser();
    }

    @ApiOperation(value = "通过ID获取一个众包工作者")
    @GetMapping("/worker/detail")
    public CsUser getWorkerById(@RequestParam long id) {
        return manageService.getUserById(id);
    }

    @ApiOperation(value = "获取一个worker的工作历史情况")
    @GetMapping("worker/userTask/list")
    public ArrayList<CsUserTask> getUserTaskByWorker(@RequestParam long id) {
        return manageService.getUserTaskByWorkerId(id);
    }

}
        d、TaskController
主要对于任务信息进行管理和获取
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;
import springfox.documentation.annotations.ApiIgnore;

import java.util.ArrayList;

@Slf4j
@RestController
@RequestMapping(CsConst.TaskApiPrefix)
@Api(tags = "群智任务模块", value = "群智任务模块")
public class TaskController {

    @Autowired
    public TaskService taskService;

    @ApiOperation(value = "创建问题")
    @PostMapping("/question/create")
    public CsQuestion createQuestion(@RequestParam String type,
                                     @RequestParam String content,
                                     String tableContent,
                                     String conditions) {
        return taskService.createQuestion(QuestionType.valueOf(type), content, tableContent, conditions);
    }

    @ApiOperation(value = "获取问题前置条件")
    @PostMapping("question/condition")
    public String getCondition(@RequestParam long id) {
        return taskService.getCondition(id);
    }

    @ApiOperation(value = "修改问题前置条件")
    @PostMapping("/question/modify/condition")
    public CsQuestion modifyCondition(@RequestParam long id,
                                      @RequestParam String conditions) {
        return taskService.modifyCondition(id, conditions);
    }

    @ApiOperation(value = "创建任务")
    @PostMapping("/task/create")
    public CsTask createTask(@RequestParam String name,
                             @RequestParam String description,
                             String requirement,
                             String taskBody,
                             @RequestParam String taskType,
                             long expireTime,
                             @RequestParam double totalBudget,
                             @RequestParam double reward,
                             @RequestParam(required = false, value = "list[]") ArrayList<Long> qIds,
                             @RequestParam ArrayList<String> domains) {
        return taskService.createTask(name, description, requirement, taskBody, taskType, totalBudget, reward, expireTime,
                qIds, domains);
    }

    @ApiOperation(value = "修改任务")
    @PostMapping("/task/modify")
    public CsTask modifyTask(@RequestParam long id,
                             String name,
                             String description,
                             String requirement,
                             String taskBody) {
        return taskService.modifyTask(id, name, description, requirement, taskBody);
    }

//    @PostMapping("/userTask/create")
//    public UserTask createUserTask() {
//        return taskService.createUserTask();
//    }

    @ApiOperation(value = "获取推荐任务")
    @GetMapping("/recommendTask/list")
    public ArrayList<CsTask> getRecommendTask(@ApiIgnore @Session CSUserSession session,
                                              Double longitude,
                                              Double latitude,
                                              String domain,
                                              String taskType) {
        return taskService.getRecommendTasks();
    }

    @ApiOperation(value = "全部任务")
    @GetMapping("/task/list")
    public ArrayList<CsTask> getAllTask(@ApiIgnore @Session CSUserSession session) {
        return taskService.getAllTasks();
    }

    @ApiOperation(value = "获取用户已接受任务")
    @GetMapping("/acceptedTask/list")
    public ArrayList<CsUserTask> getUserTask(@ApiIgnore @Session CSUserSession session,
                                             @RequestParam String status) {
        return taskService.getUserTaskList(session, status);
    }

    @ApiOperation(value = "用户接受任务")
    @PostMapping("/task/accept")
    public CsUserTask userAcceptTask(@ApiIgnore @Session CSUserSession session,
                                     @RequestParam long taskId) {
        return taskService.userAcceptTask(session, taskId);
    }

    @ApiOperation(value = "用户完成任务")
    @PostMapping("/userTask/finish")
    public CsUserTask userFinishTask(@ApiIgnore @Session CSUserSession session,
                                     @RequestParam long utId,
                                     @RequestParam String response) {
        return taskService.userFinishTask(session, utId, response);
    }

    @ApiOperation(value = "根据任务ID获取任务详情")
    @GetMapping("/task/detail")
    public TaskDetail getTaskById(@RequestParam long csTaskId) {
        return taskService.getTaskDetail(csTaskId);
    }

    @ApiOperation(value = "根据ID获取用户任务详情")
    @GetMapping("userTask/detail")
    public CsUserTask getUserTaskById(@RequestParam long utId) {
        return taskService.getUserTaskDetail(utId);
    }

    @ApiOperation(value = "根据任务ID获取任务完成情况")
    @GetMapping("task/result")
    public TaskResult getTaskResult(@RequestParam long taskId) {
        return taskService.getTaskResult(taskId);
    }

    @ApiOperation(value = "返还群智判断题综合结果")
    @PostMapping("task/result/update")
    public boolean updateJudgeResult(@RequestParam long taskId,
                                     @RequestParam String judgeResultList) {
        return taskService.updateJudgeResult(taskId, judgeResultList);
    }

}
    2、service
    对应接口的函数情况
        a、CsUserInfoService
import cn.edu.pku.sei.i3city.common.data.cs.CsUser;
import cn.edu.pku.sei.i3city.common.data.cs.CSUserSession;
import cn.edu.pku.sei.i3city.common.data.cs.CsUserDomain;
import cn.edu.pku.sei.i3city.common.enumerate.cs.Sex;
import cn.edu.pku.sei.i3city.common.enumerate.cs.TaskStatus;
import cn.edu.pku.sei.i3city.cs.storage.CSUserStorage;
import cn.edu.pku.sei.i3city.cs.storage.TaskStorage;
import cn.edu.pku.sei.i3city.cs.storage.UserDomainStorage;
import cn.edu.pku.sei.i3city.cs.storage.UserTaskStorage;
import cn.edu.pku.sei.i3city.cs.view.CsUserDetail;
import cn.edu.pku.sei.i3city.cs.view.CsUserDomainView;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.ArrayList;
import java.util.List;

@Service
public class CsUserInfoService {

    @Autowired
    private TaskStorage taskStorage;

    @Autowired
    private UserTaskStorage userTaskStorage;

    @Autowired
    private CSUserStorage csUserStorage;

    @Autowired
    private UserDomainStorage userDomainStorage;

    public CsUserDetail getCurrent(CSUserSession session) {
        CsUser csUser = session.getCsUser();
        CsUserDetail userDetail = new CsUserDetail();
        userDetail.setCsUser(csUser);
        userDetail.setDoingTaskCount(userTaskStorage.findByCsUserAndTaskStatus(session.getCsUser(), TaskStatus.Doing).size());
        userDetail.setFinishTaskCount(userTaskStorage.findByCsUserAndTaskStatus(session.getCsUser(), TaskStatus.Finished).size());
        return userDetail;
    }

    public CsUser modifyUserInfo(CSUserSession session, String sex, String tel, String email, String major, String career, String interest) {
        CsUser csUser = session.getCsUser();
        if (sex.equals("Male")) {
            csUser.setSex(Sex.MALE);
        } else if (sex.equals("Female")) {
            csUser.setSex(Sex.FEMALE);
        }
        csUser.setTel(tel);
        csUser.setEmail(email);
        csUser.setMajor(major);
        csUser.setCareer(career);
        csUser.setInterest(interest);
        return csUserStorage.save(csUser);
    }

    public CsUser setUserDomain(CSUserSession session, ArrayList<String> domains) {
        CsUser csUser = session.getCsUser();
        csUser.setDomains(domains);
        return csUserStorage.save(csUser);
    }

    public boolean setUserActive(CSUserSession session, boolean active) {
        CsUser csUser = session.getCsUser();
        csUser.setActive(active);
        csUserStorage.save(csUser);
        return true;
    }

    public List<CsUserDomainView> getUserDomainData(String domain) {
        List<CsUserDomain> userDomains = userDomainStorage.findByDomain(domain);
        List<CsUser> users = csUserStorage.findByActiveEquals(true);
        List<CsUserDomainView> userDomainViews = new ArrayList<>();
        users.forEach(user -> {
            CsUserDomainView view = new CsUserDomainView();
            view.setCsUser(user);
            view.setDomain(domain);
            if (userDomains != null) {
                userDomains.forEach(userDomain -> {
                    if (userDomain.getCsUser() == user) {
                        view.setScore(userDomain.getScore());
                    }
                });
            }
            userDomainViews.add(view);
        });
        return userDomainViews;
    }

}
        b、CsUserService

import cn.edu.pku.sei.i3city.common.data.cs.CsUser;
import cn.edu.pku.sei.i3city.cs.storage.CSUserSessionStorage;
import cn.edu.pku.sei.i3city.cs.storage.CSUserStorage;
import lombok.extern.slf4j.Slf4j;
import org.json.JSONObject;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Slf4j
@Service
public class CsUserService {

    @Autowired
    private CSUserStorage csUserStorage;

    @Autowired
    private CSUserSessionStorage csUserSessionStorage;

    private CsUser getUserByOpenId(String openId) {
        CsUser csUser = csUserStorage.findByOpenId(openId);
        if (csUser != null) {
            return csUser;
        } else {
            return null;
        }
    }

    private CsUser createUser(String openId, String data) {
        CsUser csUser = getUserByOpenId(openId);
        if (csUser == null) {
            csUser = new CsUser();
            csUser.setCreateTime(System.currentTimeMillis());
            csUser.setOpenId(openId);
            csUser.setActive(true);
            csUser.setScore(0);
        }
        JSONObject jsonObject = new JSONObject(data);
        String nickName = jsonObject.getString("nickName");
        csUser.setWxUserInfo(data);
        csUser.setName(nickName);
        return csUserStorage.save(csUser);
    }
}

        c、ManageService

import cn.edu.pku.sei.i3city.common.data.cs.CsUser;
import cn.edu.pku.sei.i3city.common.data.cs.CsTask;
import cn.edu.pku.sei.i3city.common.data.cs.CsUserTask;
import cn.edu.pku.sei.i3city.cs.storage.CSUserStorage;
import cn.edu.pku.sei.i3city.cs.storage.QuestionStorage;
import cn.edu.pku.sei.i3city.cs.storage.TaskStorage;
import cn.edu.pku.sei.i3city.cs.storage.UserTaskStorage;
import cn.edu.pku.sei.i3city.cs.view.ManagementTask;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.ArrayList;

@Service
public class ManageService {

    @Autowired
    public TaskStorage taskStorage;

    @Autowired
    public TaskService taskService;

    @Autowired
    public CSUserStorage csUserStorage;

    @Autowired
    public UserTaskStorage userTaskStorage;

    @Autowired
    public QuestionStorage questionStorage;

    public ArrayList<CsTask> getAllTasks() {
        ArrayList<CsTask> res = taskStorage.findByExpireTimeIsGreaterThanEqualAndTotalCountGreaterThan(System.currentTimeMillis() - 86400000, -1);
        if (res == null) {
            return new ArrayList<>();
        } else {
            return res;
        }
    }

    public ManagementTask getTaskDetail(long id) {
        ManagementTask managementTask = new ManagementTask();
        CsTask csTask = taskStorage.findById(id);
        managementTask.setCsTask(csTask);
        managementTask.setCsQuestions(questionStorage.findByCsTask(csTask));
        return managementTask;
    }

    public CsUserTask getUserTaskDetail(long id) {
        return userTaskStorage.findById(id);
    }

    public ArrayList<CsUser> getAllUser() {
        ArrayList<CsUser> csUsers = new ArrayList<>();
        csUserStorage.findAll().forEach(csUsers::add);
        return csUsers;
    }

    public ArrayList<CsUserTask> getUserTaskByTaskId(long id) {
        CsTask csTask = taskStorage.findById(id);
        ArrayList<CsUserTask> csUserTasks = new ArrayList<>();
        if (csTask != null) {
            csUserTasks = userTaskStorage.findByCsTask(csTask);
        }
        return csUserTasks;
    }

    public CsUser getUserById(long id) {
        return csUserStorage.findById(id);
    }

    public ArrayList<CsUserTask> getUserTaskByWorkerId(long id) {
        CsUser csUser = csUserStorage.findById(id);
        if (csUser != null) {
            return taskService.refreshStatus(userTaskStorage.findByCsUser(csUser));
        }
        return new ArrayList<>();
    }

}

        d、TaskService

import cn.edu.pku.sei.i3city.common.data.cs.*;
import cn.edu.pku.sei.i3city.common.enumerate.cs.QuestionType;
import cn.edu.pku.sei.i3city.common.enumerate.cs.TaskStatus;
import cn.edu.pku.sei.i3city.common.enumerate.cs.TaskType;
import cn.edu.pku.sei.i3city.cs.storage.*;
import cn.edu.pku.sei.i3city.cs.view.TaskDetail;
import lombok.extern.slf4j.Slf4j;
import org.json.JSONArray;
import org.json.JSONObject;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import javax.transaction.Transactional;
import java.util.*;
import java.util.stream.Collectors;

@Slf4j
@Service
public class TaskService {

    @Autowired
    public UserTaskStorage userTaskStorage;

    @Autowired
    public CSUserStorage csUserStorage;

    @Autowired
    private TaskStorage taskStorage;

    @Autowired
    private QuestionStorage questionStorage;

    @Autowired
    private UserDomainStorage userDomainStorage;

    @Transactional
    public CsQuestion createQuestion(QuestionType type, String content, String tableContent, String conditions) {
        CsQuestion csQuestion = new CsQuestion();
        csQuestion.setQuestionType(type);
        csQuestion.setContent(content);
        csQuestion.setTableContent(tableContent);
        csQuestion.setConditions(conditions);
//        log.info(tableContent);
        return questionStorage.save(csQuestion);
    }

    public String getCondition(long id) {
        CsQuestion csQuestion = questionStorage.findById(id);
        return csQuestion.getConditions();
    }

    @Transactional
    public CsQuestion modifyCondition(long questionId, String conditions) {
        CsQuestion csQuestion = questionStorage.findById(questionId);
        csQuestion.setConditions(conditions);
        return questionStorage.save(csQuestion);
    }

    @Transactional
    public CsTask createTask(String name, String description, String requirement, String taskBody, String taskType,
                             double totalBudget, double reward, long expireTime, ArrayList<Long> qIds, ArrayList<String> domains) {
        // 创建任务，如果任务是push给用户的，需要将任务分配给用户
        long now = System.currentTimeMillis();
        // 10天
        if (expireTime < now + 10 * 86400000) {
            expireTime = now + 10 * 86400000;
        }
        CsTask csTask = new CsTask();
        csTask.setCreateTime(now);
        csTask.setExpireTime(expireTime);
        csTask.setTaskType(TaskType.valueOf(taskType));
        csTask.setName(name);
        csTask.setTitle(name);
        csTask.setAssigner("管理员");
        csTask.setRequirement(requirement);
        csTask.setDescription(description);
        csTask.setLatitude(0);
        csTask.setLongitude(0);
        csTask.setTotalCount((int)(totalBudget/reward));
        csTask.setTotalBudget(totalBudget);
        csTask.setReward(reward);
        csTask.setTaskBody(taskBody);
        csTask.setDomains(domains);
        final CsTask tmpCsTask = taskStorage.save(csTask);
        if (qIds == null) {
            qIds = new ArrayList<Long>();
        }
        ArrayList<CsQuestion> csQuestions = questionStorage.findByIdIn(qIds);
        csQuestions.forEach(item -> {
            item.setCsTask(tmpCsTask);
            questionStorage.save(item);
        });
        if (csTask.getTaskType() == TaskType.PUSH) {
            // 需要系统进行分配
            workerSelection(csTask);
        }
        return tmpCsTask;
    }

    private void workerSelection(CsTask csTask) {
        List<CsUser> csUsers = csUserStorage.findByActiveEquals(true);
//        ArrayList<CSUser> csUsers = new ArrayList<>();
//        csUserStorage.findAll().forEach(csUsers::add);
        HashMap<CsUser, Double> user2score = new HashMap<>();
        ArrayList<CsUser> userHasRecord = (ArrayList<CsUser>) csUsers.stream().filter(item -> {
            List<String> userDomains = item.getDomains();
            List<String> taskDomains = csTask.getDomains();
            // find domains user has finished task
            List<CsUserDomain> userDomains1 = userDomainStorage.findByCsUserAndRightCountGreaterThan(item, 0);
            Set<String> domainSet = new HashSet<>();
            HashMap<String, CsUserDomain> domainDetail = new HashMap<>();
            userDomains1.forEach(domain -> {
                domainSet.add(domain.getDomain());
                domainDetail.put(domain.getDomain(), domain);
            });
            if (userDomains != null) {
                domainSet.addAll(userDomains);
            }
            double score = 0;
            boolean flag = false;
            int totalCount = 0;
            for (String domain: taskDomains) {
                if (domainSet.contains(domain)) {
                    CsUserDomain tmpDomain = domainDetail.get(domain);
                    totalCount += tmpDomain.getComplete();
                    score += tmpDomain.getScore() * tmpDomain.getComplete();
                    flag = true;
                }
            }
            if (totalCount > 0) {
                score /= totalCount;
            }
            user2score.put(item, score);
            return flag;
        }).collect(Collectors.toList());
        userHasRecord.sort((CsUser u1, CsUser u2) -> {
            if (user2score.get(u1) - user2score.get(u2) > 0) {
                return 1;
            } else {
                return -1;
            }
        });
        ArrayList<CsUser> userNoRecord = (ArrayList<CsUser>) csUsers.stream().filter(item -> !userHasRecord.contains(item)).collect(Collectors.toList());
//        log.info(String.valueOf(userHasRecord.size()));
//        log.info(String.valueOf(userNoRecord.size()));
        userHasRecord.forEach(item -> createUserTask(item, csTask));
        userNoRecord.forEach(item -> createUserTask(item, csTask));
    }

    @Transactional
    public CsTask modifyTask(long id, String name, String description, String requirement, String taskBody) {
        CsTask csTask = taskStorage.findById(id);
        if (!name.isEmpty()) {
            csTask.setName(name);
        }
        if (!description.isEmpty()) {
            csTask.setDescription(description);
        }
        if (!requirement.isEmpty()) {
            csTask.setRequirement(requirement);
        }
        if (!taskBody.isEmpty()) {
            csTask.setTaskBody(taskBody);
        }
        return taskStorage.save(csTask);
    }

    @Transactional
    public CsUserTask createUserTask(CsUser csUser, CsTask csTask) {
        int currCount = csTask.getTotalCount();
        if (currCount < 1)
            return null;
        csTask.setTotalCount(csTask.getTotalCount() - 1);
        CsUserTask csUserTask = new CsUserTask();
        long currentTime = System.currentTimeMillis();
        csUserTask.setCreateTime(currentTime);
        csUserTask.setExpireTime(currentTime + 86400000 * 3);
        csUserTask.setCsUser(csUser);
        csUserTask.setCsTask(csTask);
        csUserTask.setTaskStatus(TaskStatus.Doing);
        taskStorage.save(csTask);
        List<String> domains = csTask.getDomains();
        domains.forEach(domain -> {
            CsUserDomain userDomain;
            userDomain = userDomainStorage.findByCsUserAndDomain(csUser, domain);
            if (userDomain != null) {
                userDomain.setAssign(userDomain.getAssign() + 1);
            } else {
                userDomain = new CsUserDomain();
                userDomain.setAssign(0);
                userDomain.setComplete(0);
                userDomain.setRightCount(0);
                userDomain.setCsUser(csUser);
                userDomain.setDomain(domain);
            }
            userDomainStorage.save(userDomain);
        });
        // todo send mp message
        return userTaskStorage.save(csUserTask);
    }

    public ArrayList<CsTask> getAllTasks() {
        return taskStorage.findByExpireTimeIsGreaterThanEqualAndTotalCountGreaterThan(System.currentTimeMillis() - 86400000, 0);
    }

    public ArrayList<CsTask> getRecommendTasks() {
        return taskStorage.findByExpireTimeIsGreaterThanEqualAndTotalCountGreaterThan(System.currentTimeMillis() - 86400000, 0);
    }

    @Transactional
    public CsUserTask userAcceptTask(CSUserSession session, long taskId) {
        CsUser csUser = session.getCsUser();
        CsTask csTask = taskStorage.findById(taskId);
        return createUserTask(csUser, csTask);
    }

    @Transactional
    public CsUserTask userFinishTask(CSUserSession session, long utId, String response) {
        CsUserTask csUserTask = userTaskStorage.findById(utId);
        List<String> domains = csUserTask.getCsTask().getDomains();
        domains.forEach(domain -> {
            CsUserDomain userDomain = userDomainStorage.findByCsUserAndDomain(session.getCsUser(), domain);
            if (userDomain != null) {
                userDomain.setComplete(userDomain.getComplete() + 1);
                userDomainStorage.save(userDomain);
            }
        });
        //if (session.getCsUser().equals(csUserTask.getCsUser())) {
        CsUser csUser = csUserTask.getCsUser();
        csUser.setScore(csUser.getScore() + 1);
        csUserStorage.save(csUser);
        long now = System.currentTimeMillis();
        if (now < csUserTask.getExpireTime()) {
            csUserTask.setTaskStatus(TaskStatus.Finished);
            csUserTask.setResponse(response);
            long createTime = csUserTask.getCsTask().getCreateTime();
            if (now-createTime <= 1*86400000) {
                csUser.setScore(csUser.getScore() + 1);
                csUserStorage.save(csUser);
            }
            return userTaskStorage.save(csUserTask);
        }
        //}
        return null;
    }

    public TaskDetail getTaskDetail(long csTaskId) {
        CsTask csTask = taskStorage.findById(csTaskId);
        ArrayList<CsQuestion> csQuestions = questionStorage.findByCsTask(csTask);
        TaskDetail taskDetail = new TaskDetail();
        taskDetail.setCsTask(csTask);
        taskDetail.setCsQuestions(csQuestions);
        return taskDetail;
    }

    public ArrayList<CsUserTask> getUserTaskList(CSUserSession session, String status) {
        TaskStatus taskStatus = TaskStatus.valueOf(status);
        ArrayList<CsUserTask> csUserTasks = userTaskStorage.findByCsUserAndTaskStatus(session.getCsUser(), taskStatus);
        refreshStatus(csUserTasks);
        csUserTasks = userTaskStorage.findByCsUserAndTaskStatus(session.getCsUser(), taskStatus);
        return csUserTasks;
    }

    public CsUserTask getUserTaskDetail(long utId) {
        return userTaskStorage.findById(utId);
    }

    @Transactional
    public ArrayList<CsUserTask> refreshStatus(ArrayList<CsUserTask> csUserTasks) {
        csUserTasks.forEach(item -> {
            long now = System.currentTimeMillis();
            if (now > item.getExpireTime() && item.getTaskStatus() == TaskStatus.Doing) {
                item.setTaskStatus(TaskStatus.Expired);
                userTaskStorage.save(item);
            }
        });
        return csUserTasks;
    }


    public TaskResult getTaskResult(long taskId) {
        log.info("获取任务" + taskId + "结果");
        CsTask csTask = taskStorage.findById(taskId);
        ArrayList<CsQuestion> csQuestions = questionStorage.findByCsTask(csTask);
        ArrayList<CsUserTask> csUserTasks = userTaskStorage.findByCsTaskAndTaskStatus(csTask, TaskStatus.Finished);
        ArrayList<QuestionResult> questionResults = new ArrayList<>();
//        log.info(String.valueOf(questions.size()));
        csQuestions.forEach(item -> {
            questionResults.add(new QuestionResult(item));
        });
//        log.info(String.valueOf(questionResults.size()));
        csUserTasks.forEach(item -> {
            JSONArray jsonArray = new JSONArray(item.getResponse());
            for (int i = 0; i < jsonArray.length(); ++i) {
                JSONObject tmp = jsonArray.getJSONObject(i);
                String qType = tmp.getString("type");
//                log.info(tmp.getString("type"));
                if (qType.equals("JUDGE")) {
                    boolean res = tmp.getBoolean("res");
                    QuestionResult curr = questionResults.get(i);
                    if (res) {
                        curr.setPositiveCount(curr.getPositiveCount() + 1);
                    } else {
                        curr.setNegativeCount(curr.getNegativeCount() + 1);
                    }
                    questionResults.set(i, curr);
//                    log.info(String.valueOf(res));
                }
            }
        });
        TaskResult taskResult = new TaskResult();
        taskResult.setQuestionResults(questionResults);
        taskResult.setCsTask(csTask);
        return taskResult;
    }

    public boolean updateJudgeResult(long taskId, String judgeResults) {
        log.info(String.valueOf(taskId));
        log.info(judgeResults);
        CsTask csTask = taskStorage.findById(taskId);
        List<CsQuestion> csQuestions = questionStorage.findByCsTask(csTask);
        List<CsUserTask> csUserTasks = userTaskStorage.findByCsTaskAndTaskStatus(csTask, TaskStatus.Finished);
        HashMap<Long, JudgeResult> resultHashMap = new HashMap<>();
        List<String> domains = csTask.getDomains();

        JSONArray results = new JSONArray(judgeResults);
        for (int i = 0; i < results.length(); ++i) {
            JSONObject tmp = results.getJSONObject(i);
            JudgeResult judgeResult = new JudgeResult();
            judgeResult.setQuestionId(tmp.getLong("questionId"));
            judgeResult.setResult(tmp.getBoolean("result"));
            resultHashMap.put(tmp.getLong("questionId"), judgeResult);
        }

//        log.info(String.valueOf(userTasks.size()));

        csUserTasks.forEach(item -> {
            CsUser csUser = item.getCsUser();
//            log.info(item.getResponse());
            JSONArray response = new JSONArray(item.getResponse());
            int responseCount = 0;
            int rightCount = 0;
            for (int i = 0; i < response.length(); ++i) {
                CsQuestion csQuestion = csQuestions.get(i);
                JudgeResult result = resultHashMap.get(csQuestion.getId());
                JSONObject tmp = response.getJSONObject(i);
                String qType = tmp.getString("type");
                if (qType.equals("JUDGE")) {
                    responseCount += 1;
                    boolean res = tmp.getBoolean("res");
                    if (res == result.isResult()) {
                        rightCount += 1;
                    }
                }
            }
            int finalResponseCount = responseCount;
            int finalRightCount = rightCount;
            domains.forEach(domain -> {
                CsUserDomain userDomain = userDomainStorage.findByCsUserAndDomain(csUser, domain);
                userDomain.setAssign(userDomain.getAssign() + finalResponseCount);
                userDomain.setRightCount(userDomain.getRightCount() + finalRightCount);
                userDomain.setScore(userDomain.getRightCount() * 1.0 / userDomain.getAssign());
                userDomainStorage.save(userDomain);
            });
        });
        return true;
    }

}

    3、storage
主要使用的一些数据结构
        a、CSUserSessionStorage
import cn.edu.pku.sei.i3city.common.data.cs.CsUser;
import cn.edu.pku.sei.i3city.common.data.cs.CSUserSession;
import org.springframework.data.repository.CrudRepository;

import java.util.ArrayList;

public interface CSUserSessionStorage extends CrudRepository<CSUserSession, Long> {

    public CSUserSession findBySessionKey(String sessionKey);

    public CSUserSession findByToken(String token);

    public ArrayList<CSUserSession> findByCsUser(CsUser csUser);

}
        b、CSUserStorage
import cn.edu.pku.sei.i3city.common.data.cs.CsUser;
import org.springframework.data.repository.CrudRepository;

import java.util.List;

public interface CSUserStorage extends CrudRepository<CsUser, Long> {

    public CsUser findByOpenId(String openId);

    public CsUser findById(long id);

    public List<CsUser> findByActiveEquals(boolean active);

}
        c、QuestionStorage
import cn.edu.pku.sei.i3city.common.data.cs.CsQuestion;
import cn.edu.pku.sei.i3city.common.data.cs.CsTask;
import org.springframework.data.repository.CrudRepository;

import java.util.ArrayList;

public interface QuestionStorage extends CrudRepository<CsQuestion, Long> {

    public ArrayList<CsQuestion> findByCsTask(CsTask csTask);

    public ArrayList<CsQuestion> findByIdIn(ArrayList<Long> ids);

    public CsQuestion findById(long id);

}
        d、TaskStorage
import cn.edu.pku.sei.i3city.common.data.cs.CsTask;
import org.springframework.data.repository.CrudRepository;

import java.util.ArrayList;

public interface TaskStorage extends CrudRepository<CsTask, Long> {

    public ArrayList<CsTask> findByExpireTimeIsLessThanEqual(long timestamp);

    public ArrayList<CsTask> findByExpireTimeIsGreaterThanEqualAndTotalCountGreaterThan(long timestamp, int totalCount);

    public CsTask findById(long id);

}
        e、UserDomainStorage
import cn.edu.pku.sei.i3city.common.data.cs.CsUser;
import cn.edu.pku.sei.i3city.common.data.cs.CsUserDomain;
import org.springframework.data.repository.CrudRepository;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public interface UserDomainStorage extends CrudRepository<CsUserDomain, Long> {

    public List<CsUserDomain> findByCsUser(CsUser csUser);

    public List<CsUserDomain> findByDomain(String domain);

    public CsUserDomain findByCsUserAndDomain(CsUser csUser, String domain);

    public List<CsUserDomain> findByCsUserAndRightCountGreaterThan(CsUser csUser, int minCount);

}
        f、UserTaskStorage
import cn.edu.pku.sei.i3city.common.data.cs.CsUser;
import cn.edu.pku.sei.i3city.common.data.cs.CsTask;
import cn.edu.pku.sei.i3city.common.data.cs.CsUserTask;
import cn.edu.pku.sei.i3city.common.enumerate.cs.TaskStatus;
import org.springframework.data.repository.CrudRepository;
import org.springframework.stereotype.Repository;

import java.util.ArrayList;

@Repository
public interface UserTaskStorage extends CrudRepository<CsUserTask, Long> {

    public CsUserTask findById(long id);

    public ArrayList<CsUserTask> findByCsUserAndTaskStatus(CsUser csUser, TaskStatus taskStatus);

    public ArrayList<CsUserTask> findByCsTask(CsTask csTask);

    public ArrayList<CsUserTask> findByCsTaskAndTaskStatus(CsTask csTask, TaskStatus taskStatus);

    public ArrayList<CsUserTask> findByCsUser(CsUser csUser);

}
    4、view
        一些类
        a、CsUserDetail
import cn.edu.pku.sei.i3city.common.data.cs.CsUser;
import lombok.Data;

@Data
public class CsUserDetail {

    private CsUser csUser;

    private int doingTaskCount;

    private int finishTaskCount;
}

        b、CsUserDomainView
import cn.edu.pku.sei.i3city.common.data.cs.CsUser;
import lombok.Data;

@Data
public class CsUserDomainView {

    private CsUser csUser;

    private String domain;

    private double Score;
}
        c、ManagemenTask
import cn.edu.pku.sei.i3city.common.data.cs.CsQuestion;
import cn.edu.pku.sei.i3city.common.data.cs.CsTask;
import lombok.Data;

import java.util.ArrayList;

@Data
public class ManagementTask {

    public CsTask csTask;

    public ArrayList<CsQuestion> csQuestions;

}
        d、TaskDetail
import cn.edu.pku.sei.i3city.common.data.cs.CsQuestion;
import cn.edu.pku.sei.i3city.common.data.cs.CsTask;
import lombok.Data;

import java.util.ArrayList;

@Data
public class TaskDetail {

    private CsTask csTask;

    private ArrayList<CsQuestion> csQuestions;

}
