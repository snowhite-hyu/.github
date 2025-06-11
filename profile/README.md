# 사보타지 🧌
팀 백설공주의 사보타지 프로토타입 구현 프로젝트 ^_^  
-> 배포된 링크는 [여기!](https://dev.snowhite-hyu.duckdns.org/) 

### 🔎 개요

🗓️  프로젝트 기간 :     2025.04.15~ 2025.06.10  
👥  참여 인원 :        7명   
🖥️  플랫폼 :     웹 (safari) 


### 🗂️ 최종 산출물

- [Project Charter](https://fixed-jelly-e4a.notion.site/Project-Charter-1ba6a93f7cbc80e9a668e0785564a4e6?source=copy_link) : 프로젝트의 목적, 범위, 주요 이해관계자, 일정, 예산 등을 간략히 요약한 문서입니다.

- [SRS](https://fixed-jelly-e4a.notion.site/SRS-1c16a93f7cbc80eeb072c59274b8f4fa?source=copy_link) : 시스템이 가져야 할 기능/비기능 요구사항들을 정리합니다.

- [활동 보고서](https://fixed-jelly-e4a.notion.site/20d6a93f7cbc80378f0dce977c52759f?source=copy_link) : 스프린트 회고 및 피드백을 기록하였습니다.
  
- [API 명세](https://fixed-jelly-e4a.notion.site/API-20d6a93f7cbc80838ef0ffdaf8192142?source=copy_link) : HTTP/WebSocket API 설명입니다.

- [Git Convention](https://fixed-jelly-e4a.notion.site/Git-Convention-1cf6a93f7cbc80e1905df32be2836534?source=copy_link) : 깃허브 협업에 사용된 표준화된 기록 방식입니다.
  
- [QA/QC](https://fixed-jelly-e4a.notion.site/QA-QC-20d6a93f7cbc80d7a09ec975a3a83e81?source=copy_link) : 품질 확보를 위해 여러 시나리오 상황에서의 테스트 결과입니다.

- [API 명세서](https://fixed-jelly-e4a.notion.site/API-20d6a93f7cbc80838ef0ffdaf8192142?source=copy_link) : HTTP/WebSocket API 명세가 정리되어 있습니다.
  
- [CI/CD](https://fixed-jelly-e4a.notion.site/CI-CD-20d6a93f7cbc80bda9aed0c4ad1c92b5?source=copy_link) : 자동회된 빌드/배포 파이프라인입니다.
  
- [FIGMA](https://www.figma.com/design/F4YIkyBFl8nnpVcqOKCxVS/snow-white?node-id=41-1343&t=j9gtRxDRN5XXAQVz-0)  : 프로토타입 디자인이 그려져 있습니다.
  
- [JIRA](https://snowhite.atlassian.net/jira/software/projects/SH/summary)
 : 프로젝트를 어떻게 진행했는지 관리 과정을 볼 수 있습니다.   

_* 단위 및 통합 테스트 코드는 각자의 로컬 환경에서만 테스트를 진행하였으며, GitHub에는 업로드되지 않았음을 참고해 주시기 바랍니다._

## ⚒️ TEST 방법
*테스트 진행 전, 프로토타입인 만큼 웹 페이지 새로고침, 앞/뒤로가기 기능은 삼가주시길 바랍니다!*  
*사크릿 창에서 테스트 하는 것을 권장드립니다.* 

### 로컬에서 프론트 실행

(프론트 코드가 있는 폴더에서 진행)

1. 현재 프로젝트의 package.json에 명시된 모든 패키지 설치
    
    `npm i`
    
2. vite.confif.ts 에 server 부분 추가
    
    바이트 서버 호스트 추가: 개발 용도의 서버
    
    ```bash
    import { defineConfig } from 'vite'
    import react from '@vitejs/plugin-react-swc'
    import tailwindcss from '@tailwindcss/vite'
    import biomePlugin from "vite-plugin-biome"
    import tsconfigPaths from "vite-tsconfig-paths"
    
    // https://vite.dev/config/
    export default defineConfig({
      plugins: [
        react(),
        tailwindcss(),
        biomePlugin({
          mode: 'lint',
          files: './src',
        }),
        tsconfigPaths(), 
      ],
      server: {
          allowedHosts: ['server.snowhite-hyu.local'],
      },
      build: {
        rollupOptions: {
          output: {
            manualChunks(id: string) {
              if (id.includes("axios")) {
                return "@network";
              }
              if (id.includes("node_modules")) {
                return "@vendor";
              }
            },
          },
        },
      },
    })
    ```
    
3. `.env.dev` 작성
    
    vite_api_base_url : api 요청을 보내는 주소 
    
    vite_asset_base_url : cdn

    vite_ws_base_url : websocket 요청을 보내는 주소
    
    content 전송 네트워크. 정적 데이터를 동적 데이터로 가져옴
    
    
    ```bash
    VITE_API_BASE_URL=https://server.snowhite-hyu.local/api
    VITE_ASSET_BASE_URL=http://127.0.0.1:8080
    VITE_WS_BASE_URL=wss://server.snowhite-hyu.local/ws
    ```
    
5. 프론트 실행
    
    `npm run dev`
    

---

### 로컬에서 백엔드 실행

(백엔드 코드가 있는 폴더에서 진행)

아래 환경변수를 설정하여 백엔드 코드 실행

로컬에 MySQL, Redis가 설치되어 있는 것을 전제로 함

```jsx
# Profile
PROFILE=local

# DB
MYSQL_URL={로컬 DB URL}
MYSQL_USERNAME={로컬 MYSQL USERNAME}
MYSQL_PASSWORD={로컬 MYSQL PASSWORD}

# JPA
JPA_DDL=create

# REDIS
REDIS_HOST=localhost
REDIS_PORT=6379

# JWT
JWT_SECRET=qhI6Q1ruJiuFxDVFVjF8vswHPfK8B/Dz5mkhY+0+bug\=
```

---

### 프론트 테스트

(caddy 파일에서 진행)

1. Caddy 설치 
    
    https://caddyserver.com/download
    
    - 실행파일로 만들기 (`caddy_darwin_arm64`  는 다운받은 caddy 이름)
        
        `chmod +x caddy_darwin_arm64` 
        
    - 실행권한 주기
        
        `sudo xattr -cr caddy_darwin_arm64`
        
    - caddy 폴더 만들어서 위 파일을 넣기
        
        `mkdir caddy`
        
2. `Caddyfile` 생성
        
        
        
        server.snowhite-hyu.local {
        	tls internal
        	reverse_proxy /api/* localhost:8080
        	reverse_proxy /ws/* 127.0.0.1:8080 {
        		header_up Connection "Upgrade"
        		header_up Upgrade "websocket"
            	}
        	reverse_proxy /* localhost:5173
        }
        
        
3. caddy 실행
    
     `./caddy_darwin_arm64 run --config Caddyfile`  
    

---

### 도메인 등록

(맥 설정이기 때문에 어디서든 진행 가능)

1. `/etc/hosts` 파일에 해당 도메인주소를 127.0.0.1로 설정
    
    `server.snowhite-hyu.local` 의 ip 가 `127.0.0.1` 라는 것을 알려줌
    
    `127.0.0.1       server.snowhite-hyu.local`

---

### 실행

아래 페이지에서 실행 가능  
[server.snowhite-hyu.local ](https://server.snowhite-hyu.local/main)



## 🤖 Tools
<div>
  <img src="https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react" />
  <img src="https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript" />
  <img src="https://img.shields.io/badge/Vite-646CFF?style=for-the-badge&logo=vite" />
  <img src="https://img.shields.io/badge/Axios-5A29E4?style=for-the-badge&logo=axios" />
  <img src="https://img.shields.io/badge/Zustand-000000?style=for-the-badge&logo=Zustand" />
</div>
<div>
  <img src="https://img.shields.io/badge/Spring%20WebFlux-6DB33F?style=for-the-badge&logo=spring" />
  <img src="https://img.shields.io/badge/WebSocket-35495E?style=for-the-badge&logo=websocket" />
  <img src="https://img.shields.io/badge/Redis-DC382D?style=for-the-badge&logo=redis" />
  <img src="https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql" />
</div>
<div>
  <img src="https://img.shields.io/badge/GitHub%20Actions-2088FF?style=for-the-badge&logo=githubactions" />
  <img src="https://img.shields.io/badge/Argo%20CD-EF7B4D?style=for-the-badge&logo=argo" />
  <img src="https://img.shields.io/badge/GitLab-FC6D26?style=for-the-badge&logo=gitlab" />
  <img src="https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes" />
</div>



