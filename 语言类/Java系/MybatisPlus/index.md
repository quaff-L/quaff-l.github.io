### pom依赖

```xml
<!-- mybatis-plus -->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.5.1</version>
</dependency>
<!-- 代码生成器 -->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-generator</artifactId>
    <version>3.5.2</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.apache.velocity/velocity-engine-core -->
<dependency>
    <groupId>org.apache.velocity</groupId>
    <artifactId>velocity-engine-core</artifactId>
    <version>2.3</version>
</dependency>
```

### 代码生成器

```java
FastAutoGenerator.create("url", "username", "password")
    .globalConfig(builder -> {
        builder.author("baomidou") // 设置作者
            .enableSwagger() // 开启 swagger 模式
            .fileOverride() // 覆盖已生成文件
            .outputDir("D://"); // 指定输出目录
    })
    .packageConfig(builder -> {
        builder.parent("com.baomidou.mybatisplus.samples.generator") // 设置父包名
            .moduleName("system") // 设置父包模块名
            .pathInfo(Collections.singletonMap(OutputFile.mapperXml, "D://")); // 设置mapperXml生成路径
    })
    .strategyConfig(builder -> {
        builder.addInclude("t_simple") // 设置需要生成的表名
            .addTablePrefix("t_", "c_"); // 设置过滤表前缀
    })
    .templateEngine(new FreemarkerTemplateEngine()) // 使用Freemarker引擎模板，默认的是Velocity引擎模板
    .execute();
```









模板

```java
## controller.java.vm
package ${package.Controller};


import com.baomidou.mybatisplus.extension.plugins.pagination.Page;
import org.springframework.web.bind.annotation.*;
import javax.annotation.Resource;
import java.util.List;
import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import com.quaff.tenpost.common.Result;

import ${package.Service}.${table.serviceName};
import ${package.Entity}.${entity};

#if(${restControllerStyle})
import org.springframework.web.bind.annotation.RestController;
#else
import org.springframework.stereotype.Controller;
#end
#if(${superControllerClassPackage})
import ${superControllerClassPackage};
#end

/**
 * <p>
 * $!{table.comment} 前端控制器
 * </p>
 *
 * @author ${author}
 * @since ${date}
 */
#if(${restControllerStyle})
@RestController
#else
@Controller
#end
@RequestMapping("#if(${package.ModuleName})/${package.ModuleName}#end/#if(${controllerMappingHyphenStyle})${controllerMappingHyphen}#else${table.entityPath}#end")
#if(${kotlin})
class ${table.controllerName}#if(${superControllerClass}) : ${superControllerClass}()#end

#else
#if(${superControllerClass})
public class ${table.controllerName} extends ${superControllerClass} {
#else
public class ${table.controllerName} {
#end

    @Resource
    private ${table.serviceName} ${table.entityPath}Service;

    // 新增或者更新
    @PostMapping
    public Result save(@RequestBody ${entity} ${table.entityPath}) {
        ${table.entityPath}Service.saveOrUpdate(${table.entityPath});
        return Result.success();
    }

    @DeleteMapping("/{id}")
    public Result delete(@PathVariable Integer id) {
        ${table.entityPath}Service.removeById(id);
        return Result.success();
    }

    @PostMapping("/del/batch")
    public Result deleteBatch(@RequestBody List<Integer> ids) {
        ${table.entityPath}Service.removeByIds(ids);
        return Result.success();
    }

    @GetMapping
    public Result findAll() {
        return Result.success(${table.entityPath}Service.list());
    }

    @GetMapping("/{id}")
    public Result findOne(@PathVariable Integer id) {
        return Result.success(${table.entityPath}Service.getById(id));
    }

    @GetMapping("/page")
    public Result findPage(@RequestParam Integer pageNum,
                            @RequestParam Integer pageSize) {
        QueryWrapper<${entity}> queryWrapper = new QueryWrapper<>();
        queryWrapper.orderByDesc("id");
        return Result.success(${table.entityPath}Service.page(new Page<>(pageNum, pageSize), queryWrapper));
    }

}

#end
```

```java
## entity.java.vm
package ${package.Entity};

#foreach($pkg in ${table.importPackages})
import ${pkg};
#end
#if(${swagger})
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
#end
#if(${entityLombokModel})
import lombok.Getter;
import lombok.Setter;
#if(${chainModel})
import lombok.experimental.Accessors;
#end
#end

/**
 * <p>
 * $!{table.comment}
 * </p>
 *
 * @author ${author}
 * @since ${date}
 */
#if(${entityLombokModel})
@Getter
@Setter
  #if(${chainModel})
@Accessors(chain = true)
  #end
#end
#if(${table.convert})
@TableName("${schemaName}${table.name}")
#end
#if(${swagger})
@ApiModel(value = "${entity}对象", description = "$!{table.comment}")
#end
#if(${superEntityClass})
public class ${entity} extends ${superEntityClass}#if(${activeRecord})<${entity}>#end {
#elseif(${activeRecord})
public class ${entity} extends Model<${entity}> {
#elseif(${entitySerialVersionUID})
public class ${entity} implements Serializable {
#else
public class ${entity} {
#end
#if(${entitySerialVersionUID})

    private static final long serialVersionUID = 1L;
#end
## ----------  BEGIN 字段循环遍历  ----------
#foreach($field in ${table.fields})

#if(${field.keyFlag})
#set($keyPropertyName=${field.propertyName})
#end
#if("$!field.comment" != "")
  #if(${swagger})
    @ApiModelProperty("${field.comment}")
  #else
    /**
     * ${field.comment}
     */
  #end
#end
#if(${field.keyFlag})
## 主键
  #if(${field.keyIdentityFlag})
    @TableId(value = "${field.annotationColumnName}", type = IdType.AUTO)
  #elseif(!$null.isNull(${idType}) && "$!idType" != "")
    @TableId(value = "${field.annotationColumnName}", type = IdType.${idType})
  #elseif(${field.convert})
    @TableId("${field.annotationColumnName}")
  #end
## 普通字段
#elseif(${field.fill})
## -----   存在字段填充设置   -----
  #if(${field.convert})
    @TableField(value = "${field.annotationColumnName}", fill = FieldFill.${field.fill})
  #else
    @TableField(fill = FieldFill.${field.fill})
  #end
#elseif(${field.convert})
    @TableField("${field.annotationColumnName}")
#end
## 乐观锁注解
#if(${field.versionField})
    @Version
#end
## 逻辑删除注解
#if(${field.logicDeleteField})
    @TableLogic
#end
    private ${field.propertyType} ${field.propertyName};
#end
## ----------  END 字段循环遍历  ----------

#if(!${entityLombokModel})
#foreach($field in ${table.fields})
  #if(${field.propertyType.equals("boolean")})
    #set($getprefix="is")
  #else
    #set($getprefix="get")
  #end

    public ${field.propertyType} ${getprefix}${field.capitalName}() {
        return ${field.propertyName};
    }

  #if(${chainModel})
    public ${entity} set${field.capitalName}(${field.propertyType} ${field.propertyName}) {
  #else
    public void set${field.capitalName}(${field.propertyType} ${field.propertyName}) {
  #end
        this.${field.propertyName} = ${field.propertyName};
  #if(${chainModel})
        return this;
  #end
    }
#end
## --foreach end---
#end
## --end of #if(!${entityLombokModel})--

#if(${entityColumnConstant})
  #foreach($field in ${table.fields})
    public static final String ${field.name.toUpperCase()} = "${field.name}";

  #end
#end
#if(${activeRecord})
    @Override
    public Serializable pkVal() {
  #if(${keyPropertyName})
        return this.${keyPropertyName};
  #else
        return null;
  #end
    }

#end
#if(!${entityLombokModel})
    @Override
    public String toString() {
        return "${entity}{" +
  #foreach($field in ${table.fields})
    #if($!{foreach.index}==0)
        "${field.propertyName}=" + ${field.propertyName} +
    #else
        ", ${field.propertyName}=" + ${field.propertyName} +
    #end
  #end
        "}";
    }
#end
}
```

```xml
<!-- mapper.xml.vm -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="${package.Mapper}.${table.mapperName}">

#if(${enableCache})
    <!-- 开启二级缓存 -->
    <cache type="${cacheClassName}"/>

#end
#if(${baseResultMap})
    <!-- 通用查询映射结果 -->
    <resultMap id="BaseResultMap" type="${package.Entity}.${entity}">
#foreach($field in ${table.fields})
#if(${field.keyFlag})##生成主键排在第一位
        <id column="${field.name}" property="${field.propertyName}" />
#end
#end
#foreach($field in ${table.commonFields})##生成公共字段
        <result column="${field.name}" property="${field.propertyName}" />
#end
#foreach($field in ${table.fields})
#if(!${field.keyFlag})##生成普通字段
        <result column="${field.name}" property="${field.propertyName}" />
#end
#end
    </resultMap>

#end
#if(${baseColumnList})
    <!-- 通用查询结果列 -->
    <sql id="Base_Column_List">
#foreach($field in ${table.commonFields})
        ${field.columnName},
#end
        ${table.fieldNames}
    </sql>

#end
</mapper>

```

```java
## service.java.vm
package ${package.Service};

import ${package.Entity}.${entity};
import ${superServiceClassPackage};

/**
 * <p>
 * $!{table.comment} 服务类
 * </p>
 *
 * @author ${author}
 * @since ${date}
 */
#if(${kotlin})
interface ${table.serviceName} : ${superServiceClass}<${entity}>
#else
public interface ${table.serviceName} extends ${superServiceClass}<${entity}> {

}
#end
```

```java
## servicelmpl.java.vm
package ${package.ServiceImpl};

import ${package.Entity}.${entity};
import ${package.Mapper}.${table.mapperName};
import ${package.Service}.${table.serviceName};
import ${superServiceImplClassPackage};
import org.springframework.stereotype.Service;

/**
 * <p>
 * $!{table.comment} 服务实现类
 * </p>
 *
 * @author ${author}
 * @since ${date}
 */
@Service
#if(${kotlin})
open class ${table.serviceImplName} : ${superServiceImplClass}<${table.mapperName}, ${entity}>(), ${table.serviceName} {

}
#else
public class ${table.serviceImplName} extends ${superServiceImplClass}<${table.mapperName}, ${entity}> implements ${table.serviceName} {

}
#end
```

