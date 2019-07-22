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
* 基本component调用和component理解
`[component {:prop1 :val1, :prop2 :val2 ...} child1 child2 ...]`
```clojure
(defn title-ul-ui [{:keys [title]}]
  [:div
   [:section
    [:h3 title]
    (into [:ul] (r/children (r/current-component)))]])

(def title-ul-ui* (r/reactify-component title-ul-ui))

(defn root []
  [:div
  [:> title-ul-ui* {:title "people"}
    [:li "Smith"]
    [:li "Taylor"]]])
```
### Google 关键词
* Reagent debug component

### 参考链接
* https://purelyfunctional.tv/guide/reagent
* [Using React Components with Reagent](https://lambdaisland.com/episodes/react-components-reagent)

### 对比React项目
* https://github.com/facebook/create-react-app

### Reagent如何调用React的组件?
* https://github.com/Day8/re-frame/blob/master/docs/Using-Stateful-JS-Components.md
* https://presumably.de/reagent-mysteries-part-4-children-and-other-props.html

### React如何调用Reagent的组件?
* nil

### 相关的库和代码例子
* https://github.com/DaveWM/reagent-material-ui [库](material-ui "0.19.0-0",两年没有更新了,只有一个宏)
* http://davewm.github.io
* https://github.com/cljsjs/packages/tree/master/material-ui

### 调试debug React & Reagent
* https://github.com/reagent-project/reagent/blob/master/src/reagent/debug.clj
* [ClojureScript macros for convenient native Javascript object access](https://github.com/binaryage/cljs-oops)

