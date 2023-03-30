axios是基于promise对Ajax的一种封装

# 请求方式

默认get请求的无参请求模式

```html
    <script>
        axios({
            // 默认使用get
            url: 'http://'
        }).then(function( response ) {
            console.log( response );
        })
    </script>
```

post

```html
    <script>
        axios({
            // post无参
            url: 'http://',
            method: 'post'
        }).then(function( response ) {
            console.log( response );
        })
    </script>
```

get带参

```html
    <script>
        axios({
            // 传参get
            url: 'http://xxxx?id=1'
        }).then(function( response ) {
            console.log( response );
        })
    </script>
```

第二种

```html
    <script>
        axios({
            // 传参get
            url: 'http://xxxx',
            params:{
                id:'1',
                name:'chb'
            }
        }).then(function( response ) {
            console.log( response );
        })
    </script>
```

post带参

```html
    <script>
        axios({
            url: 'http://xxxx',
            method:'post',
            params:{
                name:'chb'
            }
        }).then(function( response ) {
            console.log( response );
        })
    </script>
```

post请求时，用data携带的数据默认是json，所以要用params，或者服务端给接受的参数加上@requestBody

# 简写的请求方式

```html
<body>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    
    <script>
        axios.get("http://www.axios.com/").then(function(response){
            console.log(response);
        }).catch(function(error){
            comsole.log("Error: " + error)
        })
    </script>
```

带参数

```html
<body>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    
    <script>
        axios.get("http://www.axios.com/",{
            param:{
                id:1,
                name:"chb"
            }
        }).then(function(response){
            console.log(response);
        }).catch(function(error){
            comsole.log("Error: " + error)
        })
    </script>
    
</body>
```

post无参

```html
<body>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    
    <script>
        axios.post("http://www.axios.com/").then( response => {
            console.log(response);
        }).catch(function(error){
            comsole.log("Error: " + error)
        })
    </script>
    
</body>
```

post有参

```html
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
// 用第一种param带参的方式会造成数据url地址的拼接
    <script>
        axios.post("http://www.axios.com/","name=chb&age=18").then( response => {
            console.log(response);
        }).catch(function(error){
            comsole.log("Error: " + error)
        })
    </script>



<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    <script>
        // 需要后台在参数上加@RequestBody
        axios.post("http://www.axios.com/",{name="chb",age=10}).then( response => {
            console.log(response);
        }).catch(function(error){
            comsole.log("Error: " + error)
        })
    </script>
```

# 并发请求

```html
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    
    <script>
        axios.all(
        [
            axios.get("http://www.axios.com/"),
            axios.get("http://www.axios.com/",{params:{
                id:1
            }})
        ]
        ).then(res => {
            console.log(res[0]);
        }).catch(err =>{
            console.log(err)
        } )
    </script>
```

```html
<script>
    // spead会返回自动封装成对象
        axios.all(
        [
            axios.get("http://www.axios.com/"),
            axios.get("http://www.axios.com/",{params:{
                id:1
            }})
        ]
        ).then(
            axios.spread((res1,res2) => {
                console.log(res1);
                console.log(res2);
            })
        ).catch(err =>{
            console.log(err)
        } )
    </script>
```

# 全局配置

```html
<script>
        axios.default.baseURL = 'http://localhost/8080';
        axios.default.timeout = 10000;
        axio.get('getstudent').then(response => {
            console.log(response);
        })
    </script>
```

# axios实例

```html
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    
    <script>
        // 创建实例
        let newCar = axios.create({
            baseUrl: 'http://localhost:3000',
            timeout: 10000,
        });
        newCar({
            url: 'getstudent'
        }).then(res => {
            console.log(res);
        })
    </script>
```

# 拦截器

 请求拦截器

```html
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    
    <script>
        axios.interceptors.request.use(config=>{
            // 成功
            console.log(config);
            return config;
        },err=>{
            console.log(err);
        })
        axios.get("http://localhost").then(response=>{
            console.log(response);
        })
    </script>
   
```

响应拦截器

```html
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    
    <script>
        axios.interceptors.response.use(config=>{
            // 成功
            console.log(config);
            return config;
        },err=>{
            console.log(err);
        })
        axios.get("http://localhost").then(response=>{
            console.log(response);
        })
    </script>
    
```

# axios在vue中的模块封装

```js
// 封装js文件
import axios from 'axios';
export function request(config,success, fail) {

    axios({
        url: 'https://'
    }).then(res=> {
        success(res)
    }).catch(error=>{
        fail(error)
    })
}


// 调用
import {request} from './request/request'
request("http://localhost:",res=> {
  console.log(res);
},error=>{
  console.log(error);
})
```

```js
// 第二种
import axios from 'axios';
export function request(config) {
axios.default.baseURL="http://localhost"
    axios(config.url).then(res=> {
        config.success(res);
    }).catch(error=>{
        config.fail(error);
    })
}

// main.js调用
import {request} from './request/request'
request({
  url: 'https://localhost:9999',
  success: res=>{
    console.log(res);
  },
  fail:error=>{
    console.log(error);
  }
})
```

````js
// 调用
import {request} from './request/request'
request({
  url: 'https://localhost:9999',
}).then(request => {
  console.log(request);
}).catch(error => {
  console.log(error);
})


// 封装
import axios from 'axios';
export function request(config) {
let newVar = axios.create({
    url: 'http://localhost',
    timeout:5000
});
return new Promise((resolve, reject)=>{
    newVar(config).then(res=>{
        resolve(res);
    }).catch(error=>{
    reject(error);
    })
})
}

````

```js
// 封装
import axios from 'axios';
export function request(config) {
let newVar = axios.create({
    url: 'http://localhost',
    timeout:5000
});
return newVar(config)

}
// 调用
import {request} from './request/request'
request({
  url: 'https://localhost:9999',
}).then(request => {
  console.log(request);
})
```





















