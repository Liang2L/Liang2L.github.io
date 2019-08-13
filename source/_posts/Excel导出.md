---
title: "Excel导出功能"
date: 2019-07-16 17:47:11
categories:
- "小功能"
---

### 1、前端导出请求后台

### 2、后台根据查询条件、字段关联关系，生成导出文件及导出路径

Controller中 将数据、对应关系、导出文件名封装为Vo [ExcelDataVo.java](http://note.youdao.com/noteshare?id=66bbea1f66d196f04fc661bbb1110f6c&sub=6433675889F449CC8FBB6D677BDF1E7C)

```java
@ResponseBody
@RequestMapping(value = "/export.do")
public ExcelExportVo export(PageResult<DataSafetyVo> page, DataSafetyVo dataSafetyVo, HttpServletRequest request) throws Exception {
    // 查询数据列表
    List<DataSafetyVo> dataSafetyVoList = pagingList(page, dataSafetyVo).getRows();
    if (CollectionUtils.isNotEmpty(dataSafetyVoList)) {
        ExcelDataVo excelVo = new ExcelDataVo<>(dataSafetyVoList, "导出文件名", initMappingRelation());
        // 最大导出数量
        Integer exportMaxRow = CommonConstant.EXPORT_MAX_ROW;
        String template = request.getSession().getServletContext().getRealPath("/") + "download/excel.xls";
        return ExcelUtil.exportByTemplate(request, excelVo, exportMaxRow, template);
    }
    return null;
}
private String initMappingRelation() {
    return "Date:时间|Name:名称|User:用户|TotalNum:总量|FirstLevelNum:一级|SecondLevelNum:二级|ThirdLevelNum:三级|FouthLevelNum:四级";
}
```

### 3、将生成的文件名及导出路径返回前端

工具类 [ExcelUtil.java](http://note.youdao.com/noteshare?id=eb714be0520a96215a8fb68ca603186c&sub=C03EEC7902214D5FA2DD7876F71D5C07)

返回Vo [ExcelExportVo.java](http://note.youdao.com/noteshare?id=6c30ce6f5eca347fb0f024b263c6203f&sub=1DE800C8D8A54EACAD9CEDB4561274E4)

```java
public static <T> ExcelExportVo exportByTemplate(HttpServletRequest request, ExcelDataVo<T> excelVo,
                                                     Integer exportMaxRow, String template) throws Exception {
    // 导出数据
    List<Object[]> exportList = initExport(excelVo.getDataList(), excelVo.getMappingRelation());
    // 导出文件名称
    String fileName = excelVo.getFileName();
    String filePath;
    if (exportList.size() < exportMaxRow) {
        // excel导出
        filePath = exportExcel(request, exportList, fileName, template);
        return new ExcelExportVo(filePath, fileName + ".xls");
    } else {
        // zip导出
        filePath = exportZip(request, exportList, fileName, exportMaxRow, template);
        return new ExcelExportVo(filePath, fileName + ".zip");
    }
}
```

### 4、前端根据返回数据，继续请求后台进行文件下载

前端请求（以vue为例）

```vue
exportData(params).then( res => {
	if( res.status === 'success') {
		// 下载附件
		exportFn(res.message.filePath, res.message.fileName)
	} else {
		this.$message({
			showClose: true,
			message: res.message,
			type: 'error'
		})
	}
	loadingInstance.close()
}).catch( err => {
	console.log(err)
	loadingInstance.close()
})


在工具类中复制下面的方法，在需要导出的页面进行引用即可
/**
 * [exportFn 导出文件]
 * @param  {[type]} filePath [服务端导出文件的路径]
 * @param  {[type]} fileName [服务端导出文件的名称]
 * @return {[type]}          [description]
 */
export function exportFn(filePath, fileName) {
    const a = document.createElement('a')
    const baseUrl = '/home-web/common/excel/exportDownload.do'
    a.download = fileName
    a.href = encodeURI(`${baseUrl}?filePath=${filePath}&fileName=${fileName}`)
    a.style.display = 'none'
    document.body.appendChild(a)
    a.click()
    document.body.removeChild(a)
}
```

### 5、导出成功

下载公共类 [ExcelController.java](http://note.youdao.com/noteshare?id=d26a5d29f734c397432800d125f34988&sub=0985D105014741EF8D08A4E6CB4A87B6)

```java
/**
 *
 * <p>Title: exportDownload
 * <p>Description: 文件下载通用类 <em>下载完成后会删除文件目录</em>
 * @author LXL
 * @param request
 *      获取项目路径
 * @param response
 *      输出流
 * @param filePath
 *     文件路径
 * @param fileName
 *     文件名称
 * @date 2018/2/2 10:39
 */
@ResponseBody
@RequestMapping(value = "/exportDownload.do")
public void exportDownload(HttpServletRequest request, HttpServletResponse response, String filePath, String fileName) {
    try {
        downloadResponse(request, response, filePath, fileName);
    } catch (Exception e) {
        log.error("导出alone_export_data失败", e);
    }
}
```

### 6、备注

1、相关代码已给出超链接

2、工具类 [ExcelUtil.java](http://note.youdao.com/noteshare?id=eb714be0520a96215a8fb68ca603186c&sub=C03EEC7902214D5FA2DD7876F71D5C07) 中也封装了导出zip文件的方法（多个excel文件合并了一个zip文件）