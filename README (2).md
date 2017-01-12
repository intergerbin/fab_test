mmt_mk2_web
===================


----------


Documents
-------------
#### fab로 브랜치 생성하는 방법
`fab create_branch:`*branch_name*
> *branch_name* 브랜치가 생성이 되고 m2t_mk2_ *branch_name* 이름을 가진 데이터베이스가 생성됩니다.

`fab create_branch:`*branch_name*,<kbd>--no-db</kbd>
>  *branch_name* 브랜치가 생성이 되고 데이터베이스는 생성되지 않습니다.

-----------
#### fab로 브랜치 제거하는 방법
`fab delete_branch` 
브랜치의 리스트가 나오면 번호를 선택
> 지워진 브랜치의 데이터 베이스도 같이 제거합니다.

#### 로컬환경에서 MyMusicTaste 홈페이지 실행하는 방법
`git clone https://github.com/MyMusicTaste/mmt_mk2-web.git`
`cd mmt_mk2-web`
`git checkout development`
`fab create_branch:` *branch_name*
`fab docker_build_start_local:swva`
> 주소창에 *branch_name*.mmt.local 치면 MyMusicTaste 홈페이지를 볼 수 있습니다.

-----------
#### 로컬환경에서 리모트 브랜치의 MyMusicTaste 홈페이지 실행하는 방법
**NOTE** 현재 브랜치의 웹서버와 app이 빌드되어 있어야 합니다. 현재 브랜치를 커밋했는지 확인!
`fab docker_list_start`
`Select branch number : `원하는 리모트 브랜치를 선택
`Did you commit your change? [Y/n] ` Y
> 리모트 브랜치의 src 이미지, src 컨테이너와 web 컨테이너가 생성됩니다. 

-----------
####.FabricSettings
> .FabricSettings파일을 만들주세요. DNS 서버에 접근할 수 있는 정보와 key정보가 담겨 있어야 합니다. 형식은 아래와 같습니다. 앞에 키워드를 적고 뒤에 info를 적습니다.
> >DNS_HOST *DNS_HOST*
> DNS_USER  *DNS_USER*
> DNS_PASSWORD *DNS_PASSWORD*
> KEY *KEY*

-----------
#### fabfile.py
*function_name* _local() 이름을 가진 함수 는 *function_name* 함수와 거의 동일한 동작을 합니다.

##### **void** create_branch(**string** branch_name, **string** opt = "")

> *branch_name* 브랜치가 생성이 되고 opt에 아무것도 없다면 m2t_mk2_ *branch_name* 이름을 가진 데이터베이스가 생성됩니다. opt가 <kbd>--no-db</kbd>이면 데이터베이스를 생성하지 않습니다.

-----------

##### **void** delete_branch(**string** branch_name = None)
> branch_name에 입력값이 있다면 *branch_name*을 가진 브랜치와 해당 데이터베이스를 지웁니다. 
> branch_name에 입력값이 없다면 master와 developmet를 제외한 모든 브랜치가 나열됩니다. 번호를 선택하면 선택된 브랜치와 데이터베이스를 지웁니다. 현재 브랜치를 지웠다면 development브랜치로 이동하게 됩니다.


#####  **string** get_current_branchname()
> return current git branch name


#####  **void** docker_build_webserver_image_local()
> webserver을 빌드하여 mmt-webserver: *current_branch_name*이름을 가진 이미지를 생성합니다.


##### **void** docker_build_webserver_local(**int** port = 80)
> webserver image를 run하여 *current_branch_name*.mmt.webserver 이름을 가진 docker container을 생성합니다. 포트번호를 설정할 수 있습니다. 


##### **void** docker_build_src_image_local(**int** is_released = 0)
> mmt_mk2_web폴더 안에 있는 src를 빌드하여 mmt-src: *current_branch_name* 이름을 가진 이미지를 생성합니다.


##### **void** docker_build_src_local(**string** build = current_branch_name)
> 생성된 src image를 run하여 *build*.mmt.src 이름을 가진 docker container을 생성합니다. 
> .dockerignore의 "mmt_mk2/frontend_build"를 잠시 주석처리 한 후에 src container가 생성된 후에 주석(#)을 제거합니다.


#####**void** docker_build_app_image_local()
> app을 빌드하여 mmt-app:*current_branch_name*을 가진 이미지를 생성합니다. 


#####**void** docker_build_app_local()
> mmt-app:latest를 제거하고 docker_build_app_image 함수를 실행합니다.


##### **void** docker_build_start_local(**string** option = "s")
> 옵션에 따라 src, webserver, app, web을 빌드하고 run합니다. 옵션을 적는 개수와 순서는 관계없습니다.
> `fab docker_build_start_local:swav`
	>> **s**  src build & run,
	> **v**  webserver build & run
	>**a**  app build
	> **w** web run
#####**void** docker_list_start()
> 원격 저장소에 있는 모든 브랜치를 나열합니다. 나열한 브랜치를 선택할 수 있습니다. 선택한 브랜치를 `git pull`하여 src build & run하고 web을 run합니다. 내 브랜치의 mmt-app:*current_branch_name* 이미지를 공유하고 *current_branch_name*.mmt.webserver 컨테이너를 공유합니다.
> **NOTE** src build하는 과정에서 git checkout -f *branch_name* 을 하게 됩니다. 이 함수를 실행하기 전에 반드시 git commit을 해주세요
##### <i class="icon-pencil"> </i>**void** __dns_access(**string** build)
> .FabricSettings 안에 있는 host, user, password를 가져와 MMT DNS 서버에 접속합니다. DNS에 *build*.mmt.local 주소를 등록합니다.


#####**void** docker_start(**string** enviroment, **string** build = current_branch_name, **string** app_type="", **string** key="")
> newrelic servcie 컨테이너를 만드는 함수입니다.  *build*.mmt.*app-type* 이름을 가진 컨테이너를 생성합니다.
> environment는 staging과 development을 입력값으로 넣을 수 있습니다. build에 아무값도 입력하지 않는다면 현재 브랜치이름을 갖습니다. key는 .FabricSettings에서 얻을 수 있습니다.
	>> `fab docker_start:development,branch_name,web,user_key`
	> branch_name을 가진 web 컨테이너가 생성됩니다. web 컨테이너에 collectstatic함수를 실행하여 static 파일을 컨테이너 한 곳에 모아두게 됩니다. DNS server에 *branch_name*.mmt.local이 등록됩니다. 
	