# 9.9~9.13

**浏览器优化指标：**

# 11.4~11.8

**踩坑：**

1. 发布的代码显示构建错误
   - git commit -am 最佳提交不会提交未追踪文件（新文件），导致新建的组件没有成功push



**知识点：**

1. vue可以同样可以使用jsx语法，语法规则与react一致

2. mock测试，说人话就是模仿接口造点数据在接口还没开发完成时，供测试使用

3. vue组件可以设置functional配置项为true（或写在template里），是组件成为函数式组件

   - 不具有特殊逻辑，仅接受props进行渲染

   - 效率较高

   - ```html
     <!-- 函数式组件 -->  
     <template functional>  
       <div>  
         <h1>{{ props.title }}</h1>  
         <button @click="props.incrementCount">Increment</button>  
         <p>Count: {{ props.count }}</p>  
       </div>  
     </template>
     ```





**Q:**

1. Git push到远程，并新建的分支上面，本地继续进行提交，再次提交到远程分支上时，显示refuse update



# 11.11~11.15

**技巧：**

1. [a,b,c].includes(xxx)
   - 替代if ( xxx === a || xxx === b || xxx === c )
