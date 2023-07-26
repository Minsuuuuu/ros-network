ros network

![ros](https://velog.velcdn.com/images/7cmdehdrb/post/ec5704f2-4a57-4b84-b80d-a68ff978664b/image.png){: width="1000" height="1000"}

# ROS
​
# Summary
​
ROS 연결 방법 및 통신 방법에 관한 내용
​
추가적으로, ROS를 통해 스마트폰과 ROS가 설치된 Jetson Nano 간 ROS 통신에 대한 내용 
​
# ROS 설치
​
Ubuntu 기반으로 되어있는 Jetson Nano 및 Jetson Xavier에 ROS를 설치하는 방법
​
1. ROS 설치
    
    아래의 명령어를 터미널에 차례대로 작성한다. 
    
​
```powershell
$ sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
​
$ sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
​
$ sudo apt update
​
$ sudo apt install ros-melodic-desktop-full
​
$ sudo apt-get install python-pip
​
$ sudo pip install -U rosdep
​
$ sudo rosdep init
​
$ rosdep update
​
$ echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc
​
source ~/.bashrc
​
$ source /opt/ros/melodic/setup.bash
​
$ sudo apt install python-rosinstall python-rosinstall-generator python-wstool build-essential
```
​
위와 같이 설치가 완료되었다면 새로운 터미널을 열고,
​
```powershell
$ roscore
```
​
명령어를 실행해서 정상적으로 동작한다면 ROS가 정상적으로 설치된 것을 확인할 수 있다.
​
1. ROS Workspace (catkin) 설정
    
    ROS의 Workspace인 catkin을 설치하는 방법
    
    ```powershell
    $ source /opt/ros/melodic/setup.bash
    ```
    
    catkin 작업 공간 만들기
    
    ```powershell
    $ mkdir ~p ~/catkin_ws/src
    $ cd ~/catkin_ws/src
    $ catkin_init_workspace
    ```
    
    작업 공간 빌드해주기
    
    ```powershell
    $ cd ~/catkin_ws/
    $ catkin_make
    ```
    
    위 과정을 통해 ‘build’와 ‘devel’ 폴터가 생성된 것을 확인할 수 있다. 
    
    새로운 [setup.sh](http://setup.sh)파일을 쉘에 적용하기
    
    ```powershell
    $ source devel/setup.bash
    ```
    
​
# ROS 연결 방법 & 통신 방법 (개념)
​
ROS 이용 
​
- 연결 방법
    
    Master가 될 ROS에서
    
    ```powershell
    $ roscore
    ```
    
    ROS 마스터를 구동하는 명령어. 패키지들을 실행하기 전에 꼭 실행해야 한다.
    마스터는 노드와 노드 사이의 연결 및 메시지 통신을 위한 네임 서버이다. 마스터가 없으면 ROS노드 간에 통신을 할 수 없다.
    
- 통신 방법
    1. 마스터 구동: ‘roscore’ 명령어를 통해 마스터를 구동한다.
    2. 서브스크라이버 노드 구동: ‘rosrun 패키지이름 노드이름’이라는 명령어를 통해 마스터에게 노드의 이름과 토픽의 이름, 어떤 메시지 형태를 받고 싶은지 등 다양한 정보를 보내준다.
    3. 퍼블리셔 노드 구동: 구동 방식은 서브스크라이버 노드 구동 방식과 동일하다.
    4. 퍼블리셔 정보 알림: 마스터는 서브스크라이버 노드에 새로운 퍼블리셔 정보를 전해준다.
    5. 퍼블리셔 노드에 접속 요청: 마스터로부터 받은 퍼블리셔의 정보를 이용해서 TCPROS(메시지 및 서비스에 사용되는 TCP/IP 기반의 메시지 방식) 접속을 요청한다.
    6. 서브스크라이버 노드에 접속 응답: 접속 응답에 해당되는 자신의 TCP URL 주소와 포트 번호를 전송해준다.
    7. TCP 접속: TCPROS를 이용하여 퍼블리셔 노드와 직접 연결한다.
    8. 토픽 메시지 전송: 퍼블리셔 노드는 서브스크라이버 노드에 메시지를 전송한다. 토픽 방식에는 접속을 끊지 않는 이상 지속적으로 메시지를 전송한다.
    9. 서비스 요청 및 응답: 1회에 한해 접속하여, 서비스 요청 및 서비스 응답이 수행되고 서로 간의 접속을 끊는다.
        
        ![Untitled](ROS%2087404f2fa492483fb0274bd23b4bcf1f/Untitled.png)
        
​
# ROS Mobile - Jetson Nano간 ROS 통신
​
ROS를 사용할 수 있도록 만들어진 Android App인 ROS Mobile을 사용했다.
​
ROS Mobile을 이용해 Publisher - Subscriber 간 통신을 진행했다.
​
- ROS Mobile을 Master에 연결
    
    ROS Mobile에서 Master의 IP와 포트 번호(11311)를 입력하면 ‘Connected’라는 문구가 뜨면서 ROS Master와 연결된 것을 확인할 수 있다.
    
    ROS Mobile에서 DETAILS라는 곳을 클릭하고 거기에서 Button을 생성한다.
    
    Master인 Jetson Nano에서
    
    ```powershell
    $ rostopic list
    ```
    
    명령어로 ROS가 구동되는 리스트를 확인했을 때, ROS Mobile에서 버튼을 생성할 때 밑에 기본적으로 설정되어 있는 topic이 추가된 것을 확인할 수 있다.
    
    ```powershell
    $ rosnode list
    ```
    
    명령어로 ROS가 구동되는 리스트를 확인했을 때, ROS Mobile에서 생성된 Button과 관련된 노드가 추가된 것을 확인할 수 있다.
    
- ROS Mobile - Jetson Nano간 통신 테스트
    
    Jetson Nano에서 publisher.py와 subscriber.py를 생성해준다.
    
    [Publisher.py]
    
    ```python
    #!/usr/bin/env python
    import rospy
    from std_msgs.msg import Bool
    def button_publisher():
       rospy.init_node('button_publisher')
       pub = rospy.Publisher('button_topic', Bool, queue_size=10)
       rate = rospy.Rate(10) # 발행 속도 설정 (10Hz)
       while not rospy.is_shutdown():
           button_input = input("Press Enter to send True: ") # 사용자 입력 대기
           button_value = True if button_input == '' else False
           pub.publish(button_value)
           rate.sleep()
    if _name_ == '_main_':
       try:
           button_publisher()
       except rospy.ROSInterruptException:
           pass
    ```
    
    [Subscriber.py]
    
    ```python
    #!/usr/bin/env python
    import rospy
    from std_msgs.msg import Bool
    import subprocess
    def button_callback(msg):
       if msg.data:
           # 'rosbag record -a' 명령 실행
           subprocess.Popen(['rosbag', 'record', '-a'])
       else:
           # 'rosbag record' 중지
           subprocess.Popen(['rosnode', 'kill', '/record'])
    def button_subscriber():
       rospy.init_node('button_subscriber')
       rospy.Subscriber('button_topic', Bool, button_callback)
       rospy.spin()
    if _name_ == '_main_':
       try:
           button_subscriber()
       except rospy.ROSInterruptException:
           pass
    ```
    
    publisher.py와 subscriber.py를 돌리고 ROS Mobile에서 Button을 클릭하면 그에 따라 지금 현재 동작되고 있는 rosnode에 대한 message를 기록하고 버튼에서 손을 때면 기록을 중지하도록 코드를 작성했다.
    
    테스트해본 결과, 정상적으로 동작되는 것을 확인했다.
    
​
# ROS Client - Server 통신
​
Jetson AGX Xavier 2개를 이용하여 하나의 Jetson Xavier는 Client, 다른 Jetson Xavier는 Server로 하여 두 기기 간 무선으로 통신할 수 있도록 테스트했다.
​
- ROS Client Server 패키지 생성 및 설정
    
    ```powershell
    $ catkin_create_pkg ros_test std_msgs roscpp rospy
    ```
    
    패키지를 생성했다면, /catkin_ws/src/ros_test 디렉토리에서 .xml파일에 아래의 내용을 추가해준다.
    
    ```xml
    <?xml version="1.0"?>
    <package>
      <name>oroca_ros_tutorials</name>
      <version>0.1.0</version>
      <description>The oroca_ros_tutorials package</description>
    
      <maintainer email="passionvirus@gmail.com">Yoonseok Pyo</maintainer>
      <url type="website">http://oroca.org</url>
      <url type="repository">https://github.com/oroca/oroca_ros_tutorials.git</url>
      <author email="passionvirus@gmail.com">Yoonseok Pyo</author>
    
      <license>MIT</license>
    
      <buildtool_depend>catkin</buildtool_depend>
    
      <build_depend>roscpp</build_depend>
      <build_depend>std_msgs</build_depend>
      <build_depend>message_generation</build_depend>
    
      <run_depend>roscpp</run_depend>
      <run_depend>std_msgs</run_depend>
      <run_depend>message_runtime</run_depend>
    
      <export>
      </export>
    </package>
    ```
    
    CMakeLists.txt의 내용을 수정한다.
    
    ```markdown
    cmake_minimum_required(VERSION 2.8.3)
    project(ros_test)
    
    ## Find catkin and any catkin packages
    find_package(catkin REQUIRED COMPONENTS roscpp std_msgs message_generation)
    
    ## Declare ROS messages and services
    add_message_files(FILES msgTutorial.msg)
    add_service_files(FILES srvTutorial.srv)
    
    ## Generate added messages and services
    generate_messages(DEPENDENCIES std_msgs)
    
    ## Declare a catkin package
    catkin_package(
      INCLUDE_DIRS include
      LIBRARIES ros_test
      CATKIN_DEPENDS roscpp std_msgs
      DEPENDS system_lib
    )
    
    ## Build node
    include_directories(include ${catkin_INCLUDE_DIRS})
    
    add_executable(ros_tutorial_msg_publisher src/ros_tutorial_msg_publisher.cpp)
    target_link_libraries(ros_tutorial_msg_publisher ${catkin_LIBRARIES})
    add_dependencies(ros_tutorial_msg_publisher ros_test_generate_messages_cpp)
    
    add_executable(ros_tutorial_msg_subscriber src/ros_tutorial_msg_subscriber.cpp)
    target_link_libraries(ros_tutorial_msg_subscriber ${catkin_LIBRARIES})
    add_dependencies(ros_tutorial_msg_subscriber ros_test_generate_messages_cpp)
    
    add_executable(ros_tutorial_srv_server src/ros_tutorial_srv_server.cpp)
    target_link_libraries(ros_tutorial_srv_server ${catkin_LIBRARIES})
    add_dependencies(ros_tutorial_srv_server ros_test_generate_messages_cpp)
    
    add_executable(ros_tutorial_srv_client src/ros_tutorial_srv_client.cpp)
    target_link_libraries(ros_tutorial_srv_client ${catkin_LIBRARIES})
    add_dependencies(ros_tutorial_srv_client ros_test_generate_messages_cpp)
    ```
    
    메시지 파일을 작성해준다.
    
    ```powershell
    $ cd ~/catkin_ws/src/ros_test/
    $ mkdir msg
    $ cd msg
    $ gedit msgTutorial.msg
    ```
    
    서비스 파일을 작성해준다.
    
    ```powershell
    $ roscd ros_test
    $ mkdir srv
    $ cd srv
    $ gedit srvTutorial.srv
    ```
    
    msgTutoral.msg 안에 int32 메시지 형식에 data라는 이름의 메시지를 만들어준다.
    
    ```markdown
    int32 data
    ```
    
    srvTutoral.srv 안에 int64형식의 a, b라는 서비스 요청과 sum이라는 서비스 응답 요청과 응답을 구분해주는 구분자이다.
    
    ```markdown
    int64 a
    int64 b
    ---
    int64 sum
    ```
    
    서비스 서버 노드 작성
    
    ```powershell
    $ roscd ros_test
    $ src
    $ gedit ros_tutorial_srv_server.cpp
    ```
    
    [ros_tutorial_srv_server.cpp]
    
    ```cpp
    #include "ros/ros.h"                         // ROS 기본 헤더파일
    #include "ros_test/srvTutorial.h" // srvTutorial 서비스 파일 헤더 (빌드후 자동 생성됨)
    
    bool calculation(ros_test::srvTutorial::Request  &req,
             ros_test::srvTutorial::Response &res)
    {
      res.result = req.a + req.b;  // 서비스 요청시 받은 a와 b 값을 더하여 서비스 응답값에 저장한다
    
      // 서비스 요청에 사용된 a, b값의 표시 및 서비스 응답에 해당되는 result 값을 출력한다
      ROS_INFO("request: x=%ld, y=%ld", (long int)req.a, (long int)req.b);
      ROS_INFO("sending back response: [%ld]", (long int)res.result);
    
      return true;
    }
    
    int main(int argc, char **argv)                     // 노드 메인 함수
    {
      ros::init(argc, argv, "ros_tutorial_srv_server"); // 노드명 초기화
      ros::NodeHandle nh;                               // ROS 시스템과 통신을 위한 노드 핸들 선언
    
      // 서비스 서버 선언, oroca_ros_tutorials 패키지의 srvTutorial 서비스 파일을 이용한
      // 서비스 서버 ros_tutorial_service_server 를 작성한다. 서비스명은 "ros_tutorial_srv" 이며,
      // 서비스 요청이 있을경우, calculation 라는 함수를 실행하라는 설정이다
      ros::ServiceServer ros_tutorial_service_server = nh.advertiseService("ros_tutorial_srv", calculation);
    
      ROS_INFO("ready srv server!");
    
      ros::spin();  // 서비스 요청을 대기한다
    
      return 0;
    }
    ```
    
    [ros_tutorial_srv_client.cpp]
    
    ```cpp
    #include "ros/ros.h"                         // ROS 기본 헤더파일
    #include "ros_test/srvTutorial.h" // srvTutorial 서비스 파일 헤더 (빌드후 자동 생성됨)
    #include <cstdlib>                           // atoll 함수 사용을 위한 라이브러리
    
    int main(int argc, char **argv)                     // 노드 메인 함수
    {
      ros::init(argc, argv, "ros_tutorial_srv_client"); // 노드명 초기화
      if (argc != 3)                                    // 입력값 오류 처리
      {
        ROS_INFO("cmd : rosrun ros_tutorial ros_tutorial_service_client arg0 arg1");
        ROS_INFO("arg0: double number, arg1: double number");
        return 1;
      }
    
      ros::NodeHandle nh;   // ROS 시스템과 통신을 위한 노드 핸들 선언  
    
      // 서비스 클라이언트 선언, ros_test패키지의 srvTutorial 서비스 파일을 이용한
      // 서비스 클라이언트 ros_tutorial_service_client 를 작성한다. 서비스명은 "ros_tutorial_srv" 이다
      ros::ServiceClient ros_tutorial_service_client = nh.serviceClient<ros_test::srvTutorial>("ros_tutorial_srv");
    
      // srv 라는 이름으로 srvTutorial 서비스 파일을 이용하는 서비스 파일을 선언한다
      ros_test::srvTutorial srv;
    
      // 서비스 요청 값으로 노드가 실행될 때 입력으로 사용된 매개변수를 각각의 a, b에 저장한다
      srv.request.a = atoll(argv[1]);
      srv.request.b = atoll(argv[2]);
    
      // 서비스를 요청하고, 요청이 받아들여 졌을 경우, 응답값을 표시한다
      if (ros_tutorial_service_client.call(srv))
      {
        ROS_INFO("send srv, srv.Request.a and b: %ld, %ld", (long int)srv.request.a, (long int)srv.request.b);
        ROS_INFO("recieve srv, srv.Response.result: %ld", (long int)srv.response.result);
      }
      else
      {
        ROS_ERROR("Failed to call service ros_tutorial_srv");
        return 1;
      }
    
      return 0;
    }
    ```
    
    ROS 노드를 빌드해준다.
    
    ```powershell
    $ cd ~/catkin_ws
    $ catkin_make
    ```
    
    이후 서버를 먼저 실행해주고, 그 다음에 클라이언트를 실행해줘야 통신이 되도록 한다.
    
    ```powershell
    # rosrun server
    $ rosrun ros_test ros_tutorial_srv_server
    
    # rosrun client
    $ rosrun ros_test ros_tutorial_srv_client 2 3
    ```
    
    ([로봇 운영체제 강좌 : 서비스 서버 노드와 클라이언트 노드 작성 및 실행 : 네이버 카페 (naver.com)](https://cafe.naver.com/openrt/3044)



