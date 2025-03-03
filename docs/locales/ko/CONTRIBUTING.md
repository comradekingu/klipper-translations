# 클리퍼에 기여하기

클리퍼에 기여해주셔서 감사합니다! 잠시 시간을 내어 이 문서를 읽어주세요.

## 새 이슈 만들기

이슈 생성에 대한 정보는 [contact page](Contact.md)를 참조하십시오.

## pull request 제출

코드 및 문서의 기여는 github pull 요청을 통해 관리됩니다. 각 commit에는 다음과 유사한 형식의 commit 메시지가 있어야 합니다:

```
모듈: 대문자, 짧은(50자 이하) 요약

필요한 경우 더 자세한 설명 텍스트 작성. 약 75
자 정도로 줄바꿈합니다. 일부 상황에서 첫 번째 줄은 이메일
의 제목으로 처리되고 나머지 텍스트는 본문으로 처리됩니다.
본문과 요약을 구분하는 빈 줄은 중요합니다(본문을 완전히 생략하지 않는 한).
두 가지를 함께 실행하면 rebase와 같은 도구가 혼동될 수 있
습니다.

빈 줄 뒤에 추가 단락이 옵니다..

서명자: 내 이름 <myemail@example.org>
```

각 commit에 "서명자" 라인이 있는 것이 중요합니다. 이는 개발자 인증서[developer certificate of origin](developer-certificate-of-origin)에 동의함을 증명합니다. 실명을 포함해야 하며(가명이나 익명의 기여는 불가) 현재 이메일 주소를 포함해야 합니다.

## Contributing to Klipper Translations

[Klipper-translations Project](https://github.com/Klipper3d/klipper-translations) is a project dedicated to translating Klipper to different languages. [Weblate](https://hosted.weblate.org/projects/klipper/) hosts all the Gettext strings for translating and reviewing. Locales can merge into the Klipper project once they satisfy the following requirements:

- [ ] 75% Total coverage
- [ ] All titles (H1) are covered
- [ ] An updated navigation hierarchy PR in klipper-translations.

The navigation hierarchy is in `docs\_klipper3d\mkdocs.yml`.

To reduce the frustration of translating domain-specific terms and gain awareness of the ongoing translations, you can submit a PR modifying the [Klipper-translations Project](https://github.com/Klipper3d/klipper-translations) `readme.md`. Once a translation is ready, the corresponding modification to the Klipper project can be made.

If a translation already exists in the Klipper repository and no longer meets the checklist above, it will be marked out-of-date after a month without updates.

Please follow the following format for `mkdocs.yml` navigation hierarchy:

```yml
nav:
  - existing hierachy
  - <language>:
    - locales/<language code>/md file
```

Note: Currently, there isn't a method for correctly translating pictures in the documentation.
