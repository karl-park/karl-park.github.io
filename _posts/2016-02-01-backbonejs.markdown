---
layout: post
title:  "BackboneJS"
date:   2016-02-01 21:00:00
tag:
- BackboneJS
- Javascript
- Technology
technology: true
---
#### BackBoneJS
- BackBoneJS는 우선, OneQ라는 투표 서비스를 개발하며 사용하게된 프론트엔드 프레임워크이다. 우리 서비스는 주로 싱글 페이지에서 렌더링 되기에 BackBoneJS를 택하게 되었다.(물론 상대적으로 능숙하게 잘 사용할 줄 아는 개발자 동료가 있었기에 더 힘이 되었다.)
- BackboneJS는 Backbonejs.org에서 이렇게 소개되고 있다.
> Backbone.js gives structure to web applications by providing models with key-value binding and custom events, collections with a rich API of enumerable functions, views with declarative event handling, and connects it all to your existing API over a RESTful JSON interface.

- Backbonejs는 웹 어플리케이션에 다음과 같은 것들을 제공하는데 우선, 키-값 바인딩을 가지는 모델과 커스텀 이벤트, 풍부한 API를 가지는 컬렉션, 이벤트 핸들링과 RESTful Json 인터페이스에 우리의 API를 연결하는 것들이 있다.


#### BackboneJS의 구성요소
  백본의 구성요소는 다음과 같은 것들이 있었다.

- Model
- View
- Collection
- Router

##### Model
- 백본에서의 모델은 다른 MVC프레임워크와는 달리 조금은 명확한 뜻을 지니고 있다. 즉, Model은 interactive한 데이터를 가지고 있는 컴포넌트이다. 백본에서의 모델은 다음과 같이 정의되고 선언되어진다.

```javascript


	var Human = Backbone.Model.extend({
	  // If you return a string from the validate function,
	  // Backbone will throw an error
	  validate: function( attributes ){
	    if( attributes.age < 0 && attributes.name != "Dr Manhatten" ){
	      return "You can't be negative years old";
	    }
	  },
	  initialize: function(){
	    alert("Welcome to this world");
	    this.bind("error", function(model, error){
	      // We have received an error, log it, alert it or forget it :)
	      alert( error );
	    });
	  }
	});
	
	var human = new Human;
	human.set({ name: "Mary Poppins", age: -1 }); 
	// Will trigger an alert outputting the error
	
	var human = new Human;
	human.set({ name: "Dr Manhatten", age: -1 });
	// God have mercy on our souls
```


##### View
- 백본에서의 뷰는 어플리케이션 데이터 모델을 반영하여 사용자에게 보여주는데 사용된다. 뷰는 이벤트에 맞춰서 이벤트를 듣고, 반응한다. 백본에서의 뷰는 다음과 같이 정의되고 선언되어진다.
```javascript

	<script type="text/template" id="search_template">
	    <!-- Access template variables with <%= %> -->
	    <label><%= search_label %></label>
	    <input type="text" id="search_input" />
	    <input type="button" id="search_button" value="Search" />
	</script>
	
	<div id="search_container"></div>
	
	<script type="text/javascript">
	  var SearchView = Backbone.View.extend({
	    initialize: function(){
	      this.render();
	    },
	    render: function(){
	      //Pass variables in using Underscore.js Template
	      var variables = { search_label: "My Search" };
	      // Compile the template using underscore
	      var template = _.template( $("#search_template").html(), variables );
	      // Load the compiled HTML into the Backbone "el"
	      this.$el.html( template );
	    },
	    events: {
	      "click input[type=button]": "doSearch"  
	    },
	    doSearch: function( event ){
	      // Button clicked, you can access the element that was clicked with event.currentTarget
	      alert( "Search for " + $("#search_input").val() );
	    }
	  });
	
	  var search_view = new SearchView({ el: $("#search_container") });
	</script>
```

##### Collection
- 백본에서의 컬렉션은 단순하게 모델의 집합이라고 생각하면 쉬울 듯 하다. 학생 모델이 있으면 학생 모델을 여러개 가지는 컬렉션은 Class나 출석부 등이 될 수 있겠다. 백본에서의 컬렉션은 다음과 같이 선언되고 정의되어진다.

```javascript

	var Song = Backbone.Model.extend({
	  defaults: {
	    name: "Not specified",
	    artist: "Not specified"
	  },
	      initialize: function(){
	          console.log("Music is the answer");
	      }
	  });
	
	  var Album = Backbone.Collection.extend({
	  model: Song
	});
	
	var song1 = new Song({ name: "How Bizarre", artist: "OMC" });
	var song2 = new Song({ name: "Sexual Healing", artist: "Marvin Gaye" });
	var song3 = new Song({ name: "Talk It Over In Bed", artist: "OMC" });
	
	var myAlbum = new Album([ song1, song2, song3]);
	console.log( myAlbum.models ); // [song1, song2, song3]
```

##### Router
- 백본에서의 라우터는 어플리케이션의 URL을 해시태그("#")를 사용해서 라우팅하는데 사용된다. 프론트엔드단에서 라우팅을 한다는 것이 마냥 신기하기만 했는데, 사용해보니 여간 편리해보이는게 아니다. 물론 심도있게 사용해보지는 못했지만, 추후 기회가 된다면 백본의 라우팅을 좀더 파해쳐보고싶다.

```javascript

	var AppRouter = Backbone.Router.extend({    
		routes: {
	
		    "posts/:id": "getPost",
		    // <a href="http://example.com/#/posts/121">Example</a>
		
		    "download/*path": "downloadFile",
		    // <a href="http://example.com/#/download/user/images/hey.gif">Download</a>
		
		    ":route/:action": "loadView",
		    // <a href="http://example.com/#/dashboard/graph">Load Route/Action View</a>
	
		},
		
		app_router.on('route:getPost', function( id ){ 
		    alert(id); // 121 
		});
		
		app_router.on('route:downloadFile', function( path ){ 
		    alert(path); // user/images/hey.gif 
		});
		
		app_router.on('route:loadView', function( route, action ){ 
		    alert(route + "_" + action); // dashboard_graph 
		});
	});
	// Instantiate the router
	var app_router = new AppRouter;
	app_router.on('route:getPost', function (id) {
	    // Note the variable in the route definition being passed in here
	    alert( "Get post number " + id );   
	});
	app_router.on('route:defaultRoute', function (actions) {
	    alert( actions ); 
	});
	// Start Backbone history a necessary step for bookmarkable URL's
	Backbone.history.start();

```

---
### Before Starting Backbone
백본을 제대로 하기 위해서는 _ underscorejs와 $ JQuery에 대한 이해가 선행된다. DOM을 셀렉션하는 것이나, underscore의 풍부한 메소드를 사용하는 것을 숙달하면 백본을 더욱 풍성하게 사용 할 수 있을 듯 하다.