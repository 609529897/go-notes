# net/http 中的 HTTP 请求

- 基本操作方法
- 图片下载
- 复杂的 POST 请求
- Cookie
- 重定向和请求历史
- 超时设置
- 设置代理

> grequest 包可以方便快捷的进行 HTTP 操作

---

## HTTP 基本请求

`Get`

```go
func get() {
	r, err := http.Get("http://httpbin.org/get")
	if err != nil {
		panic(err)
	}
	defer func() { _ = r.Body.Close() }()
	content, err := ioutil.ReadAll(r.Body)
	if err != nil {
		panic(err)
	}
	fmt.Printf("%s", content)
}
```

`POST`

```go
func post() {
	r, err := http.Post("http://httpbin.org/post", " ", nil)
	if err != nil {
		panic(err)
	}
	defer func() { _ = r.Body.Close() }()
	content, err := ioutil.ReadAll(r.Body)
	if err != nil {
		panic(err)
	}
	fmt.Printf("%s", content)
}
```

`PUT`

```go
func put() {
	request, err := http.NewRequest(http.MethodPut, "http://httpbin.org/put", nil)
	if err != nil {
		panic(err)
	}
	r, err := http.DefaultClient.Do(request)
	if err != nil {
		panic(err)
	}
	defer func() { _ = r.Body.Close() }()
	content, err := ioutil.ReadAll(r.Body)
	if err != nil {
		panic(err)
	}
	fmt.Printf("%s", content)
}
```

`DELETE`

```GO
func del() {
	request, err := http.NewRequest(http.MethodDelete, "http://httpbin.org/delete", nil)
	if err != nil {
		panic(err)
	}
	r, err := http.DefaultClient.Do(request)
	if err != nil {
		panic(err)
	}
	defer func() { _ = r.Body.Close() }()
	content, err := ioutil.ReadAll(r.Body)
	if err != nil {
		panic(err)
	}
	fmt.Printf("%s", content)
}
```

[测试HTTP的网站]{http://httpbin.org/}

---

## Request 的设置

```go
func printBody(r *http.Response) {
	defer func() { _ = r.Body.Close() }()
	content, err := ioutil.ReadAll(r.Body)
	if err != nil {
		panic(err)
	}
	fmt.Printf("%s", content)
}

func requestByParams() {
	request, err := http.NewRequest(http.MethodGet, "http://httpbin.org/get", nil)
	if err != nil {
		panic(err)
	}
    // 设置参数
	params := make(url.Values)
	params.Add("name", "gin")
	params.Add("age", "19")
	request.URL.RawQuery = params.Encode()
	r, err := http.DefaultClient.Do(request)
	if err != nil {
		panic(err)
	}
	printBody(r)
}

func requestByHead() {
    	request, err := http.NewRequest(http.MethodGet, "http://httpbin.org/get", nil)
	if err != nil {
		panic(err)
	}
    // 设置请求头
    request.Header.Add("user-agent", "chrome")
    r, err := http.DefaultClient.Do(request)
	if err != nil {
		panic(err)
	}
	printBody(r)
}
```

---

## Response 响应信息

```go
func main() {
	r, err := http.Get("http://baidu.com")
	if err != nil {
		panic(err)
	}
	defer func() { _ = r.Body.Close() }()
	responseBody(r)
	status(r)
	header(r)
    encoding(r)
}

// 获取响应信息
func responseBody(r *http.Response) {
	content, _ := ioutil.ReadAll(r.Body)
	fmt.Printf("%s", content)
}

// 获取状态
func status(r *http.Response) {
	fmt.Println(r.StatusCode)
	fmt.Println(r.Status)
}

// 获取请求头
func header(r *http.Response) {
	fmt.Println(r.Header.Get("Content-type"))
}

// 获取编码信息
// 1.content-type 提供
// 2.html head meta 获取
// 3.猜编码信息
func encoding(r *http.Response) {
    // 可以通过网页的头部猜编码信息
    bufReader := bufio.NewReader(r.Body)
    bytes, _ = bufReader.Peek(1024) // 不会移动 reader 的读取位置
    e, _, _ := charset.DetermineEncodeing(bytes 
    , r.Header.Get("content-type"))
    fmt.Println(e)
    bodyReader := transform.NewReader(bufReader, e.NewDecoder())
    content, _ := ioutil.ReadAll(bodyReader)
    fmt.Printf("%s", content) // 编码后的响应信息
}
```

> `go get golang.org/x/net/html` 猜编码信息
>
> `go get golang.org/x/text/transform` 用于转码

---

