# 异常

异常在 1.x 版本中处理得不是太好，所以在 2.x 版本中是略有增强的。异常处理会区分两个模式，一个是生产模式，一个是非生产模式，在生产模式中，产生的异常信息会存放到: `app/storage/{date}/error.log` 中，所以在生产环境中是可以跟踪错误问题进行及时调整的。

异常存放于目录: `{bundle}/Exceptions`

### ＃系统默认异常

系统默认异常均使用 PHP 本身自带的 `\Exception` 进行处理，没有过多的封装处理。而在自定义异常中，已定义一套异常基类，自定义异常仅需要简单继承和实现方法即可实现自定义异常的工作。

因此在系统中，异常是非常轻量并且是较为稳健的。

### ＃自定义异常

上述也说道，自定义异常仅需要简单继承基础异常类即可。

```php
<?php

namespace WelcomeBundle\Exceptions;

use FastD\Debug\Exceptions\Http\HttpException;

class NotFoundException extends HttpException
{
    public function getStatusCode()
    {
        return 404;
    }

    public function getHeaders()
    {
        return parent::getHeaders(); // TODO: Change the autogenerated stub
    }

    public function getContent()
    {
        return 404;
    }
}
```

继承的基类就是 `FastD\Debug\Exceptions\Http\HttpException`。

继续根据实际情况实现对应方法: `getStatusCode`, `getHeaders`, `getContent`。

异常中三个方法均可以自定义，因此在自定义异常模板的时候，需要使用文件读取的形式进行处理。

```php
/**
 * @Route("/exception", name="base.exception")
 *
 * @throws NotFoundException
 */
public function exceptionAction()
{
    throw new NotFoundException(404);
}
```

包括如果是 API 接口类型的异常，可以定义响应头格式 `getHeaders` 方法，返回 `application/json` 文档类型头部即可。因此在 2.x 版本中异常的处理更为灵活方便。