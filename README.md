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
* 调用React组件例子: Reactable.Table
```clojure
(def reactable-table (r/adapt-react-class js/Reactable.Table))

(defn example []
  [:div
  [reactable-table
    {:data (clj->js [
                 {:name "Foo" :section 51}
                 {:name "Bar" :section 51}])}]])

;;;;;;; https://github.com/prognostic-llc/reagent-json-editor/blob/master/vendor/reagent-json-editor/src/reagent_json_editor/core.cljs#L8
(def tree-view (reagent/adapt-react-class js/TreeView))
[tree-view {:nodeLabel (str k) :key (str k)} ...]

;; http://stackoverflow.com/questions/28759108/use-predefine-react-component-from-reagent
;; https://www.reddit.com/r/Clojure/comments/4el0xi/how_to_use_an_existing_reactjs_component_with
(if (seq data)
          (into [:> js/ReactGridLayout.Responsive {:id id
                                                   :width (- width width-offset)
                                                   :row-height (/ (- height height-offset) 12)
                                                   :draggableHandle ".grid-toolbar"
                                                   :draggableCancel ".grid-content"
                                                   :autoSize true
                                                   :listenToWindowResize true
                                                   :onResize (onLayoutChange on-change data)
                                                   :onDrag (onLayoutChange on-change data)}]
                (mapv (partial GridItem item-props) data))
          [EmptyGrid {:text empty-text}])

(let [grid-layout (reagent/adapt-react-class js/ReactGridLayout)]
   (fn []
     [grid-layout {:class "grid-layout"
                  :layout final-layout
                  :cols 12
                  :draggableHandle ".grid-toolbar"
                  :draggableCancel ".grid-content"
                  :rowHeight row-height
                  :onLayoutChange #(on-layout-change state layout %)}
     [[:div.react-grid-item [:p "blah"]]
      [:div.react-grid-item [:p "blah"]]]))

```
* this 
```clojure
(ns example
    (:require [reagent.core :as r :refer [atom]]))

(defn my-div []
  (let [this (reagent/current-component)]
    (into [:div.custom (reagent/props this)]
          (reagent/children this))))

(defn call-my-div []
  [:div
   [my-div "Some text."]
   [my-div {:style {:font-weight 'bold}}
    [:p "Some other text in bold."]
    [:p "some other text"]]])
```
* print stack
```clojure
(ns main.monitor
  (:require [reagent.core :as reagent]
            ))

(defn describe-phase [f args]
  (println "component"
    (get
      (re-find
        (re-pattern "(function)(\\s)(.*)([{}])")
        (str f)) 3) "is in" (args :phase) "phase in Virtual DOM")
  )
(defn monitor [f x]
  (let [start-time (atom nil)
        render-time (atom nil)
        now #(.now js/Date)
        lifecycle (fn [args]
                    (if (or (= (args :phase) "'Will Mount'") (= (args :phase) "'Will Update'"))
                      (do
                        (reset! start-time (now))
                        (println "--------------------------------------------------------------")
                        (describe-phase f args)
                        (if (= (args :phase) "'Will Update'")
                          (.log js/console "DOM State: " (.-outerHTML (reagent/as-element (reagent/dom-node (args :this)))))
                          )
                        )
                      )
                    (if (or (= (args :phase) "'Did Mount'") (= (args :phase) "'Did Update'"))
                      (do
                        (reset! render-time (- (now) @start-time))
                        (println "--------------------------------------------------------------")
                        (describe-phase f args)
                        (.log js/console "Render Time: " @render-time "ms")
                        (.log js/console "DOM State: " (.-outerHTML (reagent/as-element (reagent/dom-node (args :this)))))
                        )
                      )
                    )

        monitored-f (with-meta #(f x)
                      {
                        :component-will-mount #(lifecycle {:phase "'Will Mount'" :this %1 :arg1 %2 :arg2 %3})
                        :component-did-mount #(lifecycle {:phase "'Did Mount'" :this %1 :arg1 %2 :arg2 %3})
                        :component-will-update #(lifecycle {:phase "'Will Update'" :this %1 :arg1 %2 :arg2 %3})
                        :component-did-update #(lifecycle {:phase "'Did Update'" :this %1 :arg1 %2 :arg2 %3})
                        :component-will-unmount #(lifecycle {:phase "'Will Unmount'" :this %1 :arg1 %2 :arg2 %3})
                        })]
    [monitored-f]))
(defn inner-component1 [comp]
  [:div "inner component 1" comp]
  )
(defn inner-component2 [comp]
  [:div "inner component 2" comp]
  )

(defn inner-component3 [comp]
  [:div "inner component 3" comp]
  )

(defn top-component []
  [:div "top component"
   [monitor inner-component1 [monitor inner-component2 [monitor inner-component3] ]]
   ]
  )

(r/render [monitor top-component] (.getElementById js/document "app"))
```
### Google 关键词
* Reagent debug component
* expand reagent to jsx
* Reagent use other component
* Reagent diff React jsx
* [Printing The component stack](https://github.com/reagent-project/reagent/issues/105)

### 参考链接
* https://purelyfunctional.tv/guide/reagent
* [Using React Components with Reagent](https://lambdaisland.com/episodes/react-components-reagent)

### 对比React项目
* https://github.com/facebook/create-react-app

### Reagent如何调用React的组件?
* https://github.com/Day8/re-frame/blob/master/docs/Using-Stateful-JS-Components.md
* https://presumably.de/reagent-mysteries-part-4-children-and-other-props.html
* [Reagent User Manual](https://gist.github.com/jmlsf/17c588deb326e538dcea6847bc66db9b)
* http://reagent-project.github.io/docs/master/InteropWithReact.html
* https://stackoverflow.com/questions/36981538/how-to-prepare-a-react-js-component-for-usage-in-clojurescript-as-an-external-re
* http://lotabout.me/orgwiki/reagent.html
* [Reagent component from scratch: Definition Table](https://medium.com/@kirill.ishanov/reagent-component-from-scratch-definition-table-5ad1d8825b87)

### React如何调用Reagent的组件?
* nil

### 创建自己的component
* https://github.com/Day8/re-frame/wiki/Creating-Reagent-Components#the-three-ways

### 相关的库和代码例子
* https://github.com/DaveWM/reagent-material-ui [库](material-ui "0.19.0-0",两年没有更新了,只有一个宏)
* http://davewm.github.io
* https://github.com/cljsjs/packages/tree/master/material-ui

### 调试debug React & Reagent
* https://github.com/reagent-project/reagent/blob/master/src/reagent/debug.clj
* [ClojureScript macros for convenient native Javascript object access](https://github.com/binaryage/cljs-oops)

### old资料
* http://blob.tomerweller.com/reagent-import-react-components-from-npm
* http://timothypratley.blogspot.com/2017/01/reagent-deep-dive-part-2-lifecycle-of.html

