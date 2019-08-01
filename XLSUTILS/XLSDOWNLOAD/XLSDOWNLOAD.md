# Java Excel API 
## Workbook�� FileUtils.transformedXls�� �̿��� ���� �ٿ�ε�

	APACHE POI��İ� Java Excel API ����� �ִµ�, Java Excel API ����� �ڵ差�� �ξ� ����.

	- Java Excel API 
		- Java : Workbook�� FileUtils.transformedXls ���.
		- XLS : ���� ���� ���ο� ���� ������ ���ε��� �ǵ��� �ڵ��� ����� �Ѵ�.

### XLS

	- sample.xls�� ���Ͽ� ������ ���� �������� ���� ���ο� �ڵ��� ����� �Ѵ�.

	```html 
	<jx:forEach items="${questList}" var="quest" varStatus="questIndex">
	```

	- ��, Java���� ������ �����͸� ������� Map���� ������, list Ȥ�� VO�� ������ ���� ���ο� �ڵ��� �� �ڵ�� ���� �ڵ����� ���ε��� �ȴ�.

	> ���� ���ο� ������ ��Ʈ �� �����, ���� �ٿ�ε� ��ư�� Ŭ���Ͽ� �����͸� �ް� �Ǵ� ���, �״�� ����ȴ�.
	

### JAVA 

	- ���� �ٿ�ε�� �� ��Ʈ���� ����

```java
    @PostMapping("/excel")
    public void jXlsSample (@ModelAttribute("xxxVo") XXXVo xxxVo, HttpServletResponse res, Model model) throws IOException {
        Resource resource = resourceLoader.getResource("classpath:jXlsTemplate/fileName.xlsx");

       // �ϵ��ڵ� ���ٴ�, ���� �޼��� ����� ���� �ִ°� ����.
        if (!resource.exists()) {
            throw new NotFoundContentsException("�ش� ������ ���������ʽ��ϴ�.");
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

	- ��Ʈ 2�� �̻� ���� ��Ű�� ���

```java
        /** ���� */
        Workbook transformedXls = FileUtils.transformXls(resource.getFile(), beans);
        transformedXls.setSheetName(0, "��������");
        transformedXls.setSheetName(1, "������");
        transformedXls.write(res.getOutputStream());
        res.getOutputStream().flush();
```

	> ��Ʈ 2�� �̻��� �� ó�� ����ϸ�Ǹ�, ��� �� ��Ʈ���� ���� ���� ���ο� �ڵ��� ����� �Ѵ�. �׷��� �˾Ƽ� map�� ��� ������ ���ε��� �ȴ�.