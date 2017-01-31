---
# You don't need to edit this file, it's empty on purpose.
# Edit theme's home layout instead if you wanna make some changes
# See: https://jekyllrb.com/docs/themes/#overriding-theme-defaults
#layout: home
---
<script type='text/javascript' src='https://cdnjs.cloudflare.com/ajax/libs/knockout/3.4.1/knockout-min.js'></script>
<script type='text/javascript' src='http://underscorejs.org/underscore-min.js'></script>
<p> <strong>Number of posts:</strong> <strong data-bind="text: posts().length">Test</strong></p>
<p> <select data-bind="options: allCategories, value: categoryFilter"></select><input data-bind='value: freeTextFilter, valueUpdate: "afterkeydown"' /></p>
<table>
<thead>
<tr>
    <th>Title(Category)</th>
    <th>Created date</th>
</tr>
</thead>
<tbody data-bind='foreach: filteredPosts'>
<tr>
<td>
    <a data-bind="attr: { href: url, title: TitlePresentation}">
    <span data-bind='text: TitlePresentation'>post</span>
</a>
</td>
<td>
    <span data-bind='text: date'>date</span>
</td>
</tr>
</tbody>
</table>

<script>
var getPostsFromJekyllAsObservable = function(){
    var posts = [];
    {% for post in site.posts %}
        var post = {};
        post.title = ko.observable("{{post.title}}");
        post.url = ko.observable("{{post.url}}");
        post.date = ko.observable("{{post.date}}");
        post.categories = [];
        {% for category in post.categories %}
            post.categories.push("{{ category }}");
        {% endfor %}
        post.TitlePresentation = ko.pureComputed(function(){
            return this.title() + '(' + this.categories.join() + ')';
        },post);
        posts.push(post);
    {% endfor %}
    return ko.observableArray(posts);
};

var getCategoriesFromJekyllAsObservable = function(){
    var allCategories = [];
    allCategories.push(self.categoryFilter());
    {% for category in site.categories %}
    allCategories.push("{{category | first}}");
    {% endfor %}
    return ko.observableArray(allCategories.sort());
};

function postsModel(){
    self = this;
    self.posts = getPostsFromJekyllAsObservable();

    self.freeTextFilter = ko.observable("");
    self.categoryFilter = ko.observable("All categories");
    self.filteredPosts = ko.pureComputed(function(){
        return _.filter(self.posts(), function(post){
            if(self.categoryFilter() == "All categories" ||
                post.categories.indexOf(self.categoryFilter()) != -1){
                if(self.freeTextFilter == "" ||
                    post.title().toLowerCase().includes(self.freeTextFilter().toLowerCase())){
                    return true;
                }
            }
        });
    },self);

    self.setCategoryFilterFromHash = function(hash){
        if(hash != ""){
            self.categoryFilter(hash.replace("#", ""));
        }
    }

    self.allCategories = getCategoriesFromJekyllAsObservable();
    self.setCategoryFilterFromHash(window.location.hash);
};
var model = new postsModel();
ko.applyBindings(model);

if( 'onhashchange' in window ) {
  window.addEventListener('hashchange', getHashValue, false);
  function getHashValue() {
      model.setCategoryFilterFromHash(window.location.hash);
  }
}

</script>