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

## 그림 첨부
2가지 방식으로 작성 할수 있으며 선택하시면 됩니다.
* [Hexo Asset Folders](https://hexo.io/ko/docs/asset-folders)
* Github Issue 이용하기

### Hexo Asset Folders
`source/images` 경로에 이미지를 추가 하시고 작성하시는 글에서 `![](/images/image.jpg)`로 사용 하시면 됩니다.
자세한 내용은 https://hexo.io/ko/docs/asset-folders 참조 하세요.

### Github Issue 이용하기
![스크린샷 2019-04-20 오후 5 30 10](https://user-images.githubusercontent.com/6037055/56454851-fac2f800-6391-11e9-828e-16d1173417bd.png)
Github Issue 등록페이지의 이미지 호스팅을 이용하는 방법이며 사용방법은 `New issue`를 클릭 후 업로드를 원하는 그림파일을 드래그앤 드롭으로 놓으면 이미지가 업로드 되면 접근 가능한 URL과 함께 이미지 노출 마크다운이 나오며 이부분을 복사 해서 사용하시면 됩니다.

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