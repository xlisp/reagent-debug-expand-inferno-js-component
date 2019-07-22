# reagent debug & expand & inferno js component 经过大量的搜索信息路径(C-r,C-d)之后,才开始写自己的论文的,开始大脑流

* 最简component和最复杂component
```clojure
(defn green-button [txt]
  [:button.green txt])
```
```clojure
(defn complex-component [a b c]
  (let [state (reagent/atom {})] ;; you can include state
    (reagent/create-class
      {:component-did-mount
       (fn [] (println "I mounted"))

       ;; ... other methods go here

       ;; name your component for inclusion in error messages
       :display-name "complex-component"

       ;; note the keyword for this method
       :reagent-render
       (fn [a b c]
         [:div {:class c}
           [:i a] " " b])})))
```
* Embedding React components from JavaScript
```clojure
[:> js/nativeJSComponent {:title "Hello" :otherProp "World"}]
```

### Google 关键词

### 参考链接
* https://purelyfunctional.tv/guide/reagent
* [Using React Components with Reagent](https://lambdaisland.com/episodes/react-components-reagent)

### 对比React项目
* https://github.com/facebook/create-react-app

### React如何调用Reagent的组件?

### 相关的库和代码例子
* https://github.com/DaveWM/reagent-material-ui [库](material-ui "0.19.0-0",两年没有更新了,只有一个宏)
* http://davewm.github.io
* https://github.com/cljsjs/packages/tree/master/material-ui

### 调试debug React & Reagent
* http://reagent-project.github.io/docs/master/reagent.debug.html

