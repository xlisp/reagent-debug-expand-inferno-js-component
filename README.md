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

### 参考链接
* https://purelyfunctional.tv/guide/reagent

