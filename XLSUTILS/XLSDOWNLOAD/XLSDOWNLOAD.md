# Java Excel API 
## Workbook과 FileUtils.transformedXls를 이용한 엑셀 다운로드

 APACHE POI방식과 Java Excel API 방식이 있는데, Java Excel API 방식이 코드량이 훨씬 적다.

 - Java Excel API 
   - Java : Workbook과 FileUtils.transformedXls 사용.
   - XLS : 엑셀 파일 내부에 따로 데이터 바인딩이 되도록 코딩을 해줘야 한다.

### XLS

  - sample.xls의 파일에 다음과 같은 형식으로 엑셀 내부에 코딩을 해줘야 한다.

  ```html 
  <jx:forEach items="${questList}" var="quest" varStatus="questIndex">
  ```

  - 즉, Java에서 엑셀에 데이터를 담기위해 Map으로 저장한, list 혹은 VO의 값들이 엑셀 내부에 코딩한 위 코드로 인해 자동으로 바인딩이 된다.

  > 엑셀 내부에 설정한 폰트 및 사이즈가, 엑셀 다운로드 버튼을 클릭하여 데이터를 받게 되는 경우, 그대로 적용된다.
	

### JAVA 

  - 엑셀 다운로드시 한 시트에만 적용

```java
    @PostMapping("/excel")
    public void jXlsSample (@ModelAttribute("xxxVo") XXXVo xxxVo, HttpServletResponse res, Model model) throws IOException {
        Resource resource = resourceLoader.getResource("classpath:jXlsTemplate/fileName.xlsx");

       // 하드코딩 보다는, 오류 메세지 상수를 만들어서 넣는게 좋다.
        if (!resource.exists()) {
            throw new NotFoundContentsException("해당 파일은 존재하지않습니다.");
        }

        // Make Test Data
        Map<String, Object> beans = new HashMap<>();
        List<XXXnVo> list = xxxService.findXXX(xxxVo);
        beans.put("list", list);

        res.setHeader("Set-Cookie", "fileDownload=false; path=/");
        res.setHeader("Cache-Control", "no-cache, no-store, must-revalidate");
        res.setHeader("Content-Disposition", String.format("attachment; filename=\"%s\"", "list.xlsx"));

        Workbook transformedXls = FileUtils.transformXls(resource.getFile(), beans);
        transformedXls.write(res.getOutputStream());
        res.getOutputStream().flush();
    }
```

  - 시트 2개 이상에 적용 시키는 방법

```java
        /** 생략 */
        Workbook transformedXls = FileUtils.transformXls(resource.getFile(), beans);
        transformedXls.setSheetName(0, "설문조사");
        transformedXls.setSheetName(1, "참여자");
        transformedXls.write(res.getOutputStream());
        res.getOutputStream().flush();
```

  > 시트 2개 이상은 위 처럼 사용하면되며, 대신 각 시트마다 엑셀 파일 내부에 코딩을 해줘야 한다. 그러면 알아서 map에 담긴 값들이 바인딩이 된다.
