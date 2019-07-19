<template>
    <div> <!-- without an outer container div this component template will not render -->
        <loading-spinner v-if="!dataLoaded"></loading-spinner>
        <transition name="fade">
            <div v-if="dataLoaded" v-cloak>
        		<div class="inside_page_header" v-bind:style="{ background: 'linear-gradient(0deg, rgba(0,0,0,0.2), rgba(0,0,0,0.2)), url(' + pageBanner.image_url + ') center center' }">
                    <div class="main_container position_relative">
                        <h2>Directory</h2>
                    </div>
                </div>
        		<div class="main_container">
        		    <div class="row">
                        <div class="col-md-12">
                            <breadcrumb></breadcrumb>
                        </div>
                    </div>
        		    <div class="row margin_40">
        		        <div class="col-md-6 clearfix">
        		            <button class="animated_btn stores_btn" @click="toggleView()">{{ toggleText }}</button>
        		            <router-link to="/map">
        		                <div class="animated_btn stores_btn">
        		                    Center Map
        		                </div>    
        		            </router-link>
        		        </div>
        		        <div class="col-md-6 clearfix">
        		            <div class="store_search">
            					<search-component :list="allStores" placeholder="Search" suggestion-attribute="name" v-model="search_result" @select="onOptionSelect" class="text-left">
            						<template slot="item" scope="option" class="manual">
            							<article class="media">
            								<p>{{ option.data.name }}</p>
            							</article>
            						</template>
            					</search-component>
            					<i class="fa fa-search"></i>
            				</div> 
            				<div class="store_category">
            					<v-select 
            					    v-model="selectedCat" 
            					    :options="dropDownCats" 
            					    :searchable="false" 
            					    :on-change="filterByCategory" 
            					    class="category-select" 
            					    placeholder="Category" 
            					    id="selectByCat"
            					    transition="dropdown-fade"
            				    ></v-select>
            				</div>
        		        </div>
        		    </div>
        			<!-- Logo View -->
        			<div v-if="logoView" class="margin_60">
            			<div v-masonry transition-duration="0.3s" item-selector=".stores-grid-item" horizontal-order="true">
                            <transition-group name="custom-classes-transition" enter-active-class="animated fadeIn" leave-active-class="animated fadeOut" tag="div">
                                <div v-masonry-tile  v-for="(store, index) in filteredStores" :key="index" class="stores-grid-item">
                            	    <div class="store_logo_container">
                            	        <router-link :to="'/stores/'+ store.slug">
                                			<div v-if="!store.no_store_logo">
                                			    <img class="transparent_logo" src="//codecloud.cdn.speedyrails.net/sites/5b1550796e6f641cab010000/image/png/1536094421888/default_background.png" alt="">
                                			    <img  class="store_img" :src="store.store_front_url_abs" :alt="store.name + 'Logo'">
                                			</div>
                                			
                                            <div v-else class="no_logo_container">
                                                <img class="transparent_logo" src="//codecloud.cdn.speedyrails.net/sites/5b1550796e6f641cab010000/image/png/1536094421888/default_background.png" alt="">
                                                <div class="no_logo_text">
                                                    <div class="store_text"><h4>{{ store.name }}</h4></div>
                                                </div>
                                            </div>
                                			<div class="store_tag" v-if="store.total_published_promos">
            									<div class="store_tag_text">Promotion</div>
            								</div>
            								<div class="store_tag" v-if="!store.total_published_promos && store.is_coming_soon_store">
            									<div class="store_tag_text">Coming Soon</div>
            								</div>
            								<div class="store_tag" v-if="!store.total_published_promos && !store.is_coming_soon_store && store.is_new_store">
            									<div class="store_tag_text">Now Open</div>
            								</div>
            								<div class="store_details">
            								    <div class="store_text"><h4>{{ store.name }}</h4></div>    
            								</div>
                                		</router-link>
                            	    </div>
                                </div>
                            </transition-group>
                        </div>
                    </div>
                    <!-- List View -->
                    <div v-if="listView" class="margin_60 listView">
                        <transition-group name="custom-classes-transition" enter-active-class="animated fadeIn" leave-active-class="animated fadeOut" tag="div">
                            <div v-for="(store, index) in filteredStores" :key="index">
                    			<div class="store_name">
                    			    <router-link :to="'/stores/'+ store.slug">
                    			        <p>{{ store.name }}</p>
                    			    </router-link>
                    			</div>
                            </div>
                        </transition-group>
                    </div>
        		</div>
	        </div>
	    </transition>
	</div>
</template>

<script>
var _extends=Object.assign||function(e){for(var t=1;t<arguments.length;t++){var i=arguments[t];for(var r in i)Object.prototype.hasOwnProperty.call(i,r)&&(e[r]=i[r])}return e};define(["Vue","vuex","vue-select","vue!search-component","masonry","vue-masonry-plugin"],function(e,t,i,r,s,o){return e.component("v-select",i.VueSelect),e.use(o["default"]),e.component("stores-m-component",{template:template,data:function(){return{dataLoaded:!1,pageBanner:null,windowWidth:0,selectedCat:null,filteredStores:null,search_result:null,query:"",toggleText:"Display as List",logoView:!0,listView:!1,dineFilter:6283}},created:function(){var e=this;this.loadData().then(function(){var t=e.findRepoByName("Directory Banner").images;e.pageBanner=null!=t?t[0]:{image_url:"//codecloud.cdn.speedyrails.net/sites/5b6dcf4e6e6f647b570a0000/image/jpeg/1529532304000/insidebanner2.jpg"},e.dataLoaded=!0,e.query=e.$route.query.category,"dining_full_service"==e.query?(e.selectedCat="Dining Full Service",e.filterByCategory):(e.selectedCat="All",e.filteredStores=e.allStores)})},watch:{$route:function(){this.query=this.$route.query.category,"dining_full_service"==this.query?(this.selectedCat="Dining Full Service",this.filterByCategory):(this.selectedCat="All",this.filteredStores=this.allStores)},selectedCat:function(){this.$nextTick(function(){e.prototype.$redrawVueMasonry()})},windowWidth:function(){this.mobile_store=this.windowWidth<=768?!0:!1}},mounted:function(){this.$nextTick(function(){window.addEventListener("resize",this.getWindowWidth),this.getWindowWidth()})},computed:_extends({},t.mapGetters(["property","timezone","processedStores","processedCategories","storesByAlphaIndex","storesByCategoryName","findCategoryById","findCategoryByName","findRepoByName"]),{allStores:function(){var e=[];return _.forEach(this.processedStores,function(t){t.no_store_logo=_.includes(t.image_url,"missing")?!0:!1,e.push(t)}),this.filteredStores=e,e},dropDownCats:function(){var e=this,t=_.filter(this.processedCategories,function(t){return _.toNumber(t.id)!==e.dineFilter});return t=_.map(t,"name"),t.unshift("All"),t},filterByCategory:function(){if(category_id=this.selectedCat,category_id="All"==category_id||null==category_id||void 0==category_id?"All":this.findCategoryByName(category_id).id,"All"==category_id)this.filteredStores=this.allStores;else{var e=(this.findCategoryById,_.filter(this.allStores,function(e){return _.indexOf(e.categories,_.toNumber(category_id))>-1}));this.filteredStores=e}var t=document.getElementById("selectByCat");t&&t.classList.remove("open")}}),methods:{loadData:function(){var e;return regeneratorRuntime.async(function(t){for(;;)switch(t.prev=t.next){case 0:return t.prev=0,t.next=3,regeneratorRuntime.awrap(Promise.all([this.$store.dispatch("getData","categories"),this.$store.dispatch("getData","repos")]));case 3:e=t.sent,t.next=9;break;case 6:t.prev=6,t.t0=t["catch"](0),console.log("Error loading data: "+t.t0.message);case 9:case"end":return t.stop()}},null,this,[[0,6]])},toggleView:function(){this.logoView?(this.toggleText="Display as Logos",this.listView=!0,this.logoView=!1):this.listView&&(this.toggleText="Display as List",this.logoView=!0,this.listView=!1)},getWindowWidth:function(){this.windowWidth=window.innerWidth},onOptionSelect:function(e){this.search_result="",this.$router.push("/stores/"+e.slug)}},beforeDestroy:function(){window.removeEventListener("resize",this.getWindowWidth)}})});
</script>