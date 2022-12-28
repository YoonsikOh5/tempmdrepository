# # 선택과제 27



## “Jenkins + docker 를 활용한 CICD구축”



### ·  에세이(실습을 통해 얻은 경험 TIL 정리) 작성

------



필수 사전 지식 학습에서 HELLO SSAFY!!!를 띄우는 것 까지는 좋았는데

과제에서는 vue 프로젝트를 배포하라고 하고 필요한 사항은 개인적으로 학습하라고 해서 좀 막막했습니다.



구글링을 해봐도 명확하게 나오는 곳도 없고 답답해서 포기하고 다른 선택과제를 하려고 했는데 어차피 프로젝트에 CI/CD하면서 도커 젠킨스 쓸껀데 사전과제 이거 하나 마무리 못하면 프로젝트때 어차피 적용 못한다는 생각으로 다시 한 번 도전해봤습니다.



#### vue 프로젝트 Jenkins + Docker로 배포하는 방법



일단 필수 사전 지식 학습에서 HELLO SSAFY!!! 를 띄웠다면 사실 거의 다 온거나 마찬가지입니다.





##### 1. 깃 레포지토리 생성 & vue 프로젝트 생성



먼저 gitlab에 새로운 레포지토리를 하나 파고 git clone해서 내 컴퓨터에 로컬 저장소를 하나 만들어줍니다.



그 폴더에 vue 프로젝트를 만듭니다

1학기때 했던 것처럼 vscode를 키고 git clone 한 폴더에 들어가서

터미널을 키고 아래 명령어를 입력하면 vue 프로젝트가 생성이 됩니다

```
npm install -g @vue/cli
```

```
vue create my-project
```

그러면 배포할 vue 프로젝트는 완성이 됐습니다

저는 배포 성공했을 때 기분 좋게 App.vue 파일의  <HelloWorld msg="도커와 젠킨스로 뷰 배포 성공" /> 이 부분을 고쳤습니다



프로젝트를 만들었으니 일단 git add . 와 git commit -m "vue 프로젝트 생성" 마지막으로 git push origin master로 깃랩에 있는 원격저장소에 푸시까지 해줍니다



깃랩에 vue 프로젝트가 정상적으로 올라간 것을 확인한 후 다음으로 넘어갑니다



##### 2. Dockerfile 만들기

참고 : [[Vue.js\] docker에 Vue.js App 올리기 (tistory.com)](https://kdeon.tistory.com/6)



생성된 my-project 폴더에 Dockerfile을 만들어야 합니다

새 파일 만들기를 한 후 이름을 Dockerfile로 작성한 뒤 아래 코드를 붙여넣습니다



> \# build stage
>
> FROM node:lts-alpine as build-stage
>
> WORKDIR /app
>
> COPY package*.json ./
>
> RUN npm install --production
>
> COPY . .
>
> RUN npm run build
>
> 
>
> 
>
> \# production stage
>
> FROM nginx:stable-alpine as production-stage
>
> COPY --from=build-stage /app/dist /usr/share/nginx/html
>
> EXPOSE 80
>
> CMD ["nginx", "-g", "daemon off;"]



Dockerfile을 만들었으니 다시 한 번 커밋하고 푸시까지 해줍니다.



그러면 이제 젠킨스로 가보겠습니다.





##### 3. 젠킨스 설정

참고 :  [젠킨스 서버 vue.js node.js 설정 (parkhyeokjin.github.io)](https://parkhyeokjin.github.io/devops/2020/10/14/JekinsNodeJsConfigure.html)



필수 사전지식과 다른 점은 jenkins에도 node.js가 필요하다는 것입니다

따라서 node.js를 설정해줘야하는데

먼저 플러그인 관리에서 NodeJS 플러그인을 설치해줍니다



그 후 Jenkins 관리 -> Global Tool Configuration -> NodeJS에서

Add NodeJS를 눌러줍니다

위 참고 블로그에 들어가면 그림까지 잘 나와 있습니다



##### 4. Freestyle project 생성

필수 사전지식때 했던 것처럼 소스 코드 관리에 Git을 누르고 위에 만들었던 원격저장소 링크를 URL에 넣어주고 Credentials도 토큰을 발급받아 등록합니다



빌드유발에서는 Build when a change is pushed to GitLab을 눌러줍니다



빌드 환경에서는 Provied Node & npm bin/ folder to PATH를 눌러줍니다



Build Steps에서 Execute shell을 누르고

> cd my-project
> docker build -t vue-app .
> docker run -d -p 80:80 --rm --name vue-app-1 vue-app

를 입력합니다



저장하고 지금 빌드를 누르면 완성입니다



url에 localhost를 입력하면 만들었던 vue project가 나타납니다.





---











> 참고 블로그 목록
>
> [젠킨스 서버 vue.js node.js 설정 (parkhyeokjin.github.io)](https://parkhyeokjin.github.io/devops/2020/10/14/JekinsNodeJsConfigure.html)
>
> [[Vue.js\] docker에 Vue.js App 올리기 (tistory.com)](https://kdeon.tistory.com/6)
