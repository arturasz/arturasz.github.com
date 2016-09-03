---
layout: post
title:  "Testing clojure post json request"
categories: coding
tags: clojure tdd json
short: >
  For the last few days I am playing with clojure and this is what made
  me stuck and solution I found (testing json post request)
---

  I am working on side project, and I've decided to use clojure for
  backend, although was planning to use ruby before.

  So I was stuck with testing json request as I was planning to test
  everything that is possible. Here is how I managed to make a POST
  request with json. Posting it here because I couldn't find any info on
  the nets at the point of writing

  the test:

    {% highlight clojure %}
  (:require [clojure.test :refer :all]
            [ring.mock.request :as mock]
            [clojure.data.json :as json]
            [YOUR.NAMESPACE.HERE :refer :all]))

(deftest first-test
  (testing "settings endpoint"
    (let [response
          (application
            (mock/content-type (mock/body
                                 (mock/request :post "/votes")
                                 (json/write-str { :vote "test" }))
                               "application/json"
                               ))]
      (is (= (:status response) 200))
      (is (= (get-in response [:body]) "{\"my-map\":\"test\"}"))
      (is (= (get-in response [:headers "Content-Type"]) "application/json; charset=utf-8")))
      {% endhighlight %}

  application routes:

    {% highlight clojure %}
  ;[ring.middleware.json :as middleware]

(def application
  (middleware/wrap-json-response (middleware/wrap-json-body
    routes)))
      {% endhighlight %}

  controller:
    {% highlight clojure %}
  ;(:require [compojure.core :refer [defroutes GET POST]]
  ;          [clojure.string :as str]
  ;          [ring.util.request :as request]
(defn putBack
  [request]
  {:body {:my-map (get-in request [:body "vote"])}})

(defroutes routes
  (POST "/votes" request (putBack request))
      {% endhighlight %}
