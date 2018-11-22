설치를 안하신분들은 최초 설치 후 사용 바람

[설치 및 프로젝트 셋팅](./install.md)

# 글 작성
`hexo new`명령어로 원하는 글제목 작성 뒤 마크다운으로 원하는 글 작성 
```bash
hexo new "Blog Post Hello World!!!"
또는
hexo n  "Blog Post Hello World!!!"
vim source/_posts/Blog-Post-Hello-World!!!.md
```

# 확인
로컬에 테스트 서버를 띄워 확인이 가능
```bash
hexo server
또는
hexo s
```

# html 생성
마크다운을 정적 웹 자원으로 만들어야 호스팅 가능(html, css, js)
```bash
hexo generate
또는
hexo g
```

# 배포
`_config.yml`에 등록된 deploy 정보로 배포가 됨

```bash
hexo deploy
또는
hexo d
```

# html 생성과 배포
명령어 한줄로 정적 웹 자원과 배포를 동시에 진행
```bash
hexo g -d
```