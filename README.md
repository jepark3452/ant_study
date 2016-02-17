# ant_study
ant study, build.xml write
## ref URL
http://www.jakartaproject.com/board-read.do?boardId=jakarta&boardNo=1114617006525&command=READ&t=1455610084487
Jakarta Ant
 
I. Ant 야 놀자~
Ant 란 "Java 기반의 빌드 도구입니다" 라고들 많이 나와있습니다
근데 이게 대체 무순 뜻이란 말인가 ㅡ.ㅡ;
 
쉽게 예기해서 환경에 구애받지 않고 간단히 java 소스를 컴파일 하고 필요한 파일이 있다면
그 파일들을 복사나 삭제하며 또한 javadoc 이나 war 같은 배포용 파일까지 생성해 주며 
필요하다면 서버에 까지 바로 적용할 수 있도록 도와주는 도구입니다.
 
또한 여러팀원들이 프로젝트를 진행할 때 새로운 팀원이 참가하더라도 
Ant 설정파일을 공유함으로써 손쉽게 참여가 가능하다는 것입니다
 
아직 사용해 보지 않았다면 얼렁 익혀서 사용해봅시다 무~지 편하답니다
 
 
II. 설치해보자
다운로드
Ant
http://ant.apache.org/bindownload.cgi

Ant manual
http://ant.apache.org/manual/index.html
http://ant.apache-korea.org/manual/index.html
 
다운받은 파일의 압축을 풀고 환경변수 및 패스를 잡아줍니다
set ANT_HOME=c:\ant
set JAVA_HOME=c:\jdk1.4.2
set PATH=%PATH%;%ANT_HOME%\bin
 
 
III. 간단한 Ant 예제
Ant를 이용하여 web application을 구성할 때 다음의 구조를 유지하기를 권장합니다

 
① build : src, web, docs에서 결과적으로 만들어진 산출물 디렉토리
② dist : build를 배포하기 위한 배포 디렉토리
③ docs : 배포판에 배포할 정적인 문서를 관리할 디렉토리
④ src : /WEB-INF/classes 에 위치할 java 소스 디렉토리
⑤ web : HTML, JSP, 이미지등의 컨텐트 디렉토리 (WEB-INF의 서브디렉토리 포함)
⑥ build.properties : build.xml에서 사용할 properties
⑦ build.xml : ant 명령으로 실행될 설정파일
 
src에 하나이상의 java 소스를 테스트로 넣어 놓으세요
자 이렇게 디렉토리를 설정하고 build.xml 을 다음 step에 따라 따라 해 BOA요 ^^&
 
STEP 1. build.xml 의 기본구조
xml을 기본적인 내용을 안다면 이해하기 쉽습니다

<project name="My Project" default="compile" basedir=".">
 <target name="clean">
   여러 작업
  </target>
 
  <target name="prepare" depends="clean">
   여러 작업
   </target>
 
   <target name="compile" depends="prepare">
    여러 작업
    </target>
</project>
하나의 build 파일은 하나의 project로 구성되며 이는 다시 여러 target으로 구성됩니다
target 이란 빌드 과정중 수행해야 할 task들을 모아놓은 job 단위 라고 보면 됩니다
compile target이라 한다면 compile에 관련된 작업들을 모아놓은 그룹이라 생각하면 쉽게 이해 될겁니다
 
STEP 2. 시~작 Ant 맛보기~
① build.xml에 다음을 입력한 후 저장 합니다

<project name="Unicorn Project" default="clear" basedir=".">
  <description>Simple Test</description>
  
  <property file="${basedir}/build.properties"/>
  
  <echo message="${catalina.home}"/>
 
   <target name="clear"/>
    
</project>
-. project
    project는 하나 이상의 target을 정의 합니다 또한 하나의 target은 task의 집합입니다
    ant를 실행할 시에 어느 타겟을 실행할 것인지 지정할 수가 있으며 (예: \ant clear)
    지정하지 않았을 경우 디폴트로 설정된 값이 사용됩니다 이부분이 default="clear"입니다
-. property
    전역변수 설정 혹은 그렇게 사용할 build.properties를 정의 합니다
    build.properties에 catalina.home을 정의하였으며 여러 환경이 변하더라도 이 값만
    변경해주면 build.xml을 수정없이 바로 실행 가능합니다
-. echo
    message 내용을 출력 합니다
-. target
    target 이란 task의 집합으로 실질적으로 실행될 코드들의 묶음입니다
    여기서는 아무 task도 없습니다
 
② build.properties에 다음을 입력 후 저장합니다

catalina.home=C:\Tomcat 5.0
catalina.home 은 변수로 사용할 것이며 그 값은 C:\Tomcat 5.0입니다
 
③ 실행
해당 디렉토리로 이동하여 도스창에서 ant 라고 칩니다

c:\예제\ant
Buildfile : build.xml
      [echo] c:\Tomcat 5.0
 
clear :
 
BUILD SUCCESSFUL
Total time : 0 seconds
 
STEP 3. 사전작업 하기~
이번 단계에서는 컴파일 하기전 전역변수 선언이나 컴파일 시 클래스 패스 설정을 해봅시다
 
① build.xml

<project name="Unicorn Project" default="prepare" basedir=".">
  <description>Simple Test</description>
  <property file="${basedir}/build.properties"/>
 
  <property name="app.name"      value="unicorn"/>
  <property name="app.path"       value="/${app.name}"/>
  <property name="app.version"   value="0.1-dev"/>
  <property name="build.home"    value="${basedir}/build"/>
  <property name="dist.home"     value="${basedir}/dist"/>
  <property name="docs.home"   value="${basedir}/docs"/>
  <property name="src.home"      value="${basedir}/src"/>
  <property name="web.home"     value="${basedir}/web"/>  
 
  <path id="compile.classpath">
    <pathelement location="${catalina.home}/common/classes"/>
    <fileset dir="${catalina.home}/common/endorsed">
      <include name="*.jar"/>
    </fileset>
    <fileset dir="${catalina.home}/common/lib">
      <include name="*.jar"/>
    </fileset>
    <pathelement location="${catalina.home}/shared/classes"/>
    <fileset dir="${catalina.home}/shared/lib">
      <include name="*.jar"/>
    </fileset>
    <fileset dir="${web.home}/WEB-INF/lib">
      <include name="*.jar"/>
    </fileset>
  </path>
 
  <target name="clean"
   description="Delete old build and dist directories">
    <delete dir="${build.home}"/>
    <delete dir="${dist.home}"/>
  </target>
 
  <target name="prepare" depends="clean">
    <mkdir dir="${build.home}"/>
    <mkdir  dir="${build.home}/WEB-INF"/>
    <mkdir  dir="${build.home}/WEB-INF/classes"/>
    <mkdir  dir="${build.home}/WEB-INF/lib"/>
    
    <copy todir="${build.home}">
       <fileset dir="${web.home}">
         <include name="**/*.jsp"/>
         <include name="**/*.css"/>
         <include name="**/*.html"/>
         <include name="**/*.js"/>
         <include name="**/*.jpg"/>
         <include name="**/*.gif"/>
         <include name="**/*.swf"/>
         <include name="**/*.xml"/>
         <include name="**/*.tld"/>
         <include name="**/*.jar"/>
       </fileset>
     </copy>
   </target>  
    
</project>
-. project
    이번에는 default 값을 prepare로 하였습니다 고로 target은 prepare가 실행될 것입니다
-. property
    역시나 build.properties를 정의하였고 여러 전역변수를 설정하였습니다
    build.home 이란 변수에는 ${basedir}/build 값이 정의되었으며
    build.home은 ${build.home}으로 사용할수 있습니다
    궁금하면 <echo message="${build.home}"/> 등으로 출력해 봅시다~
-. path
    ${catalina.home} 은 build.properties에서 정의하였다는것을 기역하실겁니다
    fileset은 파일들의 집합을 나타내는데 어떤 특정파일만 포함 할수 있거나 혹은 어느 특정파일만 제외할 수 있습니다
    특정파일만 포함하는것은 include 태그이며 그 반대는 exclude 입니다
    <fileset dir="${web.home}/WEB-INF/lib">
      <include name="*.jar"/>
    </fileset>
    이부분은 ${web.home}/WEB-INF/lib 에서 *.jar 파일만 클래스패스로 잡겠다는 의미입니다
-. target
   드디어 실질적으로 실행될 target 입니다. 
   project 속성의 default가 prepare이므로 prepare target으로 실행하기 위해 가보니  depend 속성으로 "clean"이 설정되어 있습니다
  이것은 "clean" target을 먼저 실행 후 prepare를 실행하라는 말입니다
   즉 prepare는 clean이 실행조건 입니다
   clean target은 delete 태그를 이용하여 배포할 dist 디렉토리와 산출물 디렉토리인 build를 삭제하는 작업입니다
   prepare target으로 다시 와보면 mkdir 는 당연히 디렉토리를 만들라는 뜻입니다
   copy는 todir에 다음 web.home의 모든 fileset을 copy 하라는 의미입니다 여기서도 역시나 include, exclude가 사용될 수 있겠지요
   <include name="**/*.jsp"/> 는 모든디렉토리의 *.jsp 를 카피하라는 의미입니다
    즉 web 디렉토리의 모든 실행 가능할 파일을 build로 옮기는 작업입니다 
 
② 실행

c:\예제\ant
Buildfile : build.xml
 
clean :
     [delete] : Deleting directory c:\예제\build
     [delete] : Deleting directory c:\예제\dist
 
prepare :
     [mkdir] Created dir : c:\예제\build\WEB-INF
     [mkdir] Created dir : c:\예제\build\WEB-INF\classes
     [mkdir] Created dir : c:\예제\build\WEB-INF\lib
     [copy] Copying 2 files to Deleting directory c:\예제\build
 
BUILD SUCCESSFUL
Total time : 1 seconds
 
STEP 4. 컴파일 하기
이제 컴파일할 모든 준비가 되었습니다
STEP2의 소스에 다음 target을 추가하고 project의 default 값을 compile로 수정한 후 실행해 봅시다
 

 <target name="compile" depends="prepare"
   description="Compile Java sources">
 
    <!-- Compile Java classes as necessary -->
    <javac srcdir="${src.home}"
          destdir="${build.home}/WEB-INF/classes">
        <classpath refid="compile.classpath"/>
    </javac>
 
    <!-- Copy application resources -->
    <copy  todir="${build.home}/WEB-INF/classes">
      <fileset dir="${src.home}" excludes="**/*.java"/>
    </copy>
    
 </target>
-. target
   depends가 prepare이므로 실행 순서는 clean, prepare, compile 입니다
   javac 태스크는 srcdir 인 소스디렉토리에 있는 자바소스를 desdir 쪽으로 컴파일 합니다
   classpath는 위에서 이미 설정한 compile.classpath를 사용합니다
   그 후 properties등 같은 기타 resource 들을 /WEB-INF/classes에 복사합니다
② 실행

c:\예제\ant
Buildfile : build.xml
 
clean :
     [delete] : Deleting directory c:\예제\build
     [delete] : Deleting directory c:\예제\dist
 
prepare :
     [mkdir] Created dir : c:\예제\build\WEB-INF
     [mkdir] Created dir : c:\예제\build\WEB-INF\classes
     [mkdir] Created dir : c:\예제\build\WEB-INF\lib
     [copy] Copying 2 files to Deleting directory c:\예제\build
 
compile :
     [javac] Compileing 1 source file to c:\예제\build\WEB-INF\classes
     [copy] Copying 1 file to c:\예제\build\WEB-INF\classes
 
BUILD SUCCESSFUL
Total time : 1 seconds
 
STEP 5. Javadoc 을 이용해 API를 만들어 보고 배포파일도 생성해 보자
이제 컴파일도 했으니 javadoc도 만들어 보고 war 같은 배포파일도 생성해 봅시다
STEP 3.까지의 build.xml에 아래 target을 더 추가하고 project의 default 값에 dist로 설정합니다
① build.xml

 <target name="javadoc" depends="compile"
    description="Create Javadoc API documentation">
     <mkdir dir="${dist.home}/docs/api"/>
     <javadoc sourcepath="${src.home}"
                  destdir="${dist.home}/docs/api"
                   packagenames="*">
       <classpath refid="compile.classpath"/>
     </javadoc>
   </target>
 
 <target name="dist" depends="compile,javadoc"
   description="Create binary distribution">
    <mkdir dir="${dist.home}/docs"/>
    <copy  todir="${dist.home}/docs">
      <fileset dir="${docs.home}"/>
    </copy>
 
    <jar jarfile="${dist.home}/${app.name}-${app.version}.war"
         basedir="${build.home}"/>
  </target>
-. target
   target의 실행순서는 clean, prpare, compile, javadoc, dist가 될겁니다
   javadoc target의 javadoc 태스트를 보면 java 소스가 있는 소스디렉토리와
   API를 생성할 타겟 디렉토리를 정해주면 알아서 API를 생성해 줍니다
   만들어진 API는 배포버젼의 dist디렉토리로 해주면 더 좋겠지요
 
   dist target은 배포파일인 war를 만듭니다
   필요한 문서가 있으면 docs 디렉토리를 만들어 로 복사도 하도록 합시다
   jar 태스크는 위의 방식과 같이 사용합니다
 
② 실행

c:\예제\ant
Buildfile : build.xml
 
clean :
     [delete] : Deleting directory c:\예제\build
     [delete] : Deleting directory c:\예제\dist
 
prepare :
     [mkdir] Created dir : c:\예제\build\WEB-INF
     [mkdir] Created dir : c:\예제\build\WEB-INF\classes
     [mkdir] Created dir : c:\예제\build\WEB-INF\lib
     [copy] Copying 2 files to Deleting directory c:\예제\build
 
compile :
     [javac] Compileing 1 source file to c:\예제\build\WEB-INF\classes
     [copy] Copying 1 file to c:\예제\build\WEB-INF\classes
 
javadoc :
     [mkdir] Created dir : c:\예제\dist\docs\api
     [javadoc] Generating Javadoc
     [javadoc] Javadoc execution
     [javadoc] Loading source files for package com.jakartaproject.db...
     [javadoc] Constructing Javadoc information...
     [javadoc] Standard Doclet version 1.4.1
     [javadoc] Building tree for all the packages and classes...
     [javadoc] Building index for all the packaes and classes...
     [javadoc] Building index for all classes...
 
dist :
     [jar] Building jar :Created dir : c:\예제\dist\unicorn-0.1-dev.war
 
BUILD SUCCESSFUL
Total time : 3 seconds
 
 
VI. Ant 실행
① C:\예제\ant -help
ant [options] [target [target2 [target3] ...]]

Options :
  -help                    이 메세지의 표시
  -projecthelp           프로젝트 도움 정보의 출력
  -version                버전 정보의 출력과 종료
  -diagnostics           diagnose 나 report 문제에  도움이 되는 정보의 출력.
  -quiet, -q              한층 더 메세지를 적게
  -verbose, -v          한층 더 메세지를 많게
  -debug                 디버그 정보의 출력
  -emacs                 adornments 없이 로그 정보의 생성(produce)
  -logfile <file>         로그를 지정 파일에 출력
    -l     <file>                ''
  -logger <classname>    로그 생성을 실행하기 위한 클래스
  -listener <classname>  프로젝트 청취자(listener) 역할의 class의 인스턴스를 추가
  -buildfile <file>            지정된 빌드 파일의 사용
    -file    <file>              ''
    -f       <file>              ''
  -D<property>=<value> 지정된 프로퍼티의 값의 사용
  -propertyfile <name>    모든 프로퍼티를 파일로부터 로드  (-D프로퍼티보다 전에)
  -inputhandler <class>  입력 요청(requests)를 취급하는 클래스
  -find <file>                 파일시스템의 루트로 향해 빌드파일을  검색하고 그것을 사용
 
② C:\예제\ant
현재 디렉토리에 있는 build.xml 파일을 이용해, 디폴트 타겟으로 Ant 를 실행합니다.
 
③ C:\예제\ant compile
현재 디렉토리에 있는 build.xml이 실행되며 파라미터로 compile을 지정하면 project의 default 값을 무시하고 compile target을 실행합니다 물론 depends 가 있다면 먼저 실행합니다
 
④ C:\예제\ant -buildfile test.xml
현재 디렉토리에 있는 test.xml 파일을 이용해, 디폴트 타겟으로 Ant 를 실행합니다.
 
⑤ C:\예제\ant -buildfile test.xml dist
현재 디렉토리에 있는 test.xml 파일을 이용해, dist 라는 이름의 타겟으로 Ant 를 실행합니다.
 
⑥ C:\예제\ant -buildfile test.xml -Dbuild=build/classes dist
현재 디렉토리에 있는 test.xml 파일을 이용해, dist 라는 이름의 타겟으로, build 프로퍼티에 build/classes 값을 설정해 Ant 를 실행합니다.
 
VII. 기본 프로퍼티
Ant는 다음과 같은 프로퍼티를 기본으로 제공합니다
① basedir : 기본 디렉토리 경로를 나타내며 project 태그의 basedir에 명시되어 있습니다
② ant.file : 빌드 파일의 절대 경로입니다
③ ant.version : Ant 버젼을 나타냅니다
④ ant.project.name : project 태그의 name에 명시되어있습니다
⑤ ant.java.version : 자바 버젼을 나타냅니다
⑥ 자바 시스템 프로퍼티를 사용 가능 합니다
   예) ${os.name} 참고 http://java.sun.com/j2se/1.4.2/docs/api/java/lang/System.html#getProperties()
 
<echo message="${ant.file}"/> 으로 한번 출력해 보면 이해가 쉽습니다
 
다음 시간에는 각 타스크의 종류와 상세 설정에 관해 알아봅시다
 
=============================================
본문서는 자유롭게 배포/복사 할수 있지만
이문서의 저자에 대한 언급을 삭제하시면 안됩니다
저자 : GoodBug (unicorn@jakartaproject.com)
최초 : http://www.jakartaproject.com 
=============================================

감사합니다!
