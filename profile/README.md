
## TEST 방법

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
    
    content 전송 네트워크. 정적 데이터를 동적 데이터로 가져옴
    
    
    ```bash
    VITE_API_BASE_URL=https://server.snowhite-hyu.local/api
    VITE_ASSET_BASE_URL=http://127.0.0.1:8080
    ```
    
4. 프론트 실행
    
    `npm run dev`
    

---

### 로컬에서 백엔드 실행

(백엔드 코드가 있는 폴더에서 진행)

아래 환경변수를 설정하여 백엔드 코드 실행

```jsx
# Profile
PROFILE=local

# DB
MYSQL_URL={로컬 DB URL}
MYSQL_USERNAME={로컬 MYSQL USERNAME}
MYSQL_PASSWORD={로컬 MYSQL PASSWORD}

# JPA
JPA_DDL=update

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
        
2. 프록스 규칙 생성
    - caddy 파일에서 진행
    - `Caddyfile` 생성
        
        handle_path /api/* : /api 부분을 자동으로 substring
        
        reverse_proxy : nginx 대체
        
        ```bash
        server.snowhite-hyu.local {
        	tls internal
        	handle_path /api/* {
        	    reverse_proxy localhost:8080
        	}
        	reverse_proxy /* localhost:5173
        }
        ```
        
3. caddy 실행
    
     `./caddy_darwin_arm64 run --config Caddyfile`  or
    
    `caddy run --config Caddyfile`
    

---

### 도메인 등록

(맥 설정이기 때문에 어디서든 진행 가능)

1. `/etc/hosts` 파일에 해당 도메인주소를 127.0.0.1로 설정
    
    `server.snowhite-hyu.local` 의 ip 가 `127.0.0.1` 라는 것을 알려줌
    
    `127.0.0.1       server.snowhite-hyu.local`
