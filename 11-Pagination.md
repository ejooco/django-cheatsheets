# Django Cheat Sheet 11 - Pagination
### by ejooco

**blog/views.py**

Add pagination to PostListView

Add UserPostListView

Add 2 imports
```python
from django.contrib.auth.models import User
from django.shortcuts import render, get_object_or_404

class PostListView(ListView):
    model = Post
    template_name = 'blog/home.html' 
    context_object_name = 'posts' 
    ordering = ['-date_posted'] 
    paginate_by = 5

class UserPostListView(ListView):
    model = Post
    template_name = 'blog/user_posts.html' 
    context_object_name = 'posts' 
    paginate_by = 5

    def get_queryset(self):
        user = get_object_or_404(User, username=self.kwargs.get('username'))
        return Post.objects.filter(author=user).order_by('-date_posted')
```

**blog/urls.py**

Add UserPostListView to imports

Add UserPostListView to urlpatterns

```python
from django.urls import path
from .views import (
    PostListView,
    PostDetailView,
    PostCreateView,
    PostUpdateView,
    PostDeleteView,
    UserPostListView
)
from . import views

urlpatterns = [
    path('', PostListView.as_view(), name='blog-home'),
    path('user/<str:username>', UserPostListView.as_view(), name='user-posts'),
    #<int:pk> tells django that we want to put in the primary key and that it should be an integer
    path('post/<int:pk>/', PostDetailView.as_view(), name='post-detail'),
    path('post/new/', PostCreateView.as_view(), name='post-create'),
    path('post/<int:pk>/update/', PostUpdateView.as_view(), name='post-update'),
    path('post/<int:pk>/delete/', PostDeleteView.as_view(), name='post-delete'),
    path('about/', views.about, name='blog-about'),
]
```
**blog/templates/blog/home.html**

Add pagination buttons at the bottom of page

Add routing for user href links
```
{% extends "blog/base.html" %}
{% block content %}
        {% for post in posts %}
            <article class="media content-section">
                <img class="rounded-circle article-img" src="{{ post.author.profile.image.url }}">
                <div class="media-body">
                    <div class="article-metadata">
                        <a class="mr-2" href="{% url 'user-posts' post.author.username %}">{{ post.author }}</a>
                        <small class="text-muted">{{ post.date_posted }}</small>
                    </div>
                    <h2><a class="article-title" href="{% url 'post-detail' post.id %}">{{ post.title }}</a></h2>
                    <p class="article-content">{{ post.content }}</p>
                </div>
            </article>
        {% endfor %}
        {% if is_paginated %}
            {% if page_obj.has_previous %}
                <a class="btn btn-outline-info mb-4" href="?page=1">First</a>
                <a class="btn btn-outline-info mb-4" href="?page={{ page_obj.previous_page_number }}">Previous</a>
            {% endif %}

            {% for num in page_obj.paginator.page_range %}
                {% if page_obj.number == num %}
                    <a class="btn btn-info mb-4" href="?page={{ num }}">{{ num }}</a>
                {% elif num > page_obj.number|add:'-3' and num < page_obj.number|add:'3' %}
                    <a class="btn btn-outline-info mb-4" href="?page={{ num }}">{{ num }}</a>
                {% endif %}
            {% endfor %}

            {% if page_obj.has_next %}
                <a class="btn btn-outline-info mb-4" href="?page={{ page_obj.next_page_number }}">Next</a>
                <a class="btn btn-outline-info mb-4" href="?page={{ page_obj.paginator.num_pages }}">Last</a>
            {% endif %}
        {% endif %}
{% endblock content %}
```
Create **blog/templates/blog/user_posts.html** template
```
{% extends "blog/base.html" %}
{% block content %}
        <h1 class="mb-3">Posts by {{ view.kwargs.username }} ({{ page_obj.paginator.count }})</h1>
        {% for post in posts %}
            <article class="media content-section">
                <img class="rounded-circle article-img" src="{{ post.author.profile.image.url }}">
                <div class="media-body">
                    <div class="article-metadata">
                        <a class="mr-2" href="{% url 'user-posts' post.author.username %}">{{ post.author }}</a>
                        <small class="text-muted">{{ post.date_posted }}</small>
                    </div>
                    <h2><a class="article-title" href="{% url 'post-detail' post.id %}">{{ post.title }}</a></h2>
                    <p class="article-content">{{ post.content }}</p>
                </div>
            </article>
        {% endfor %}
        {% if is_paginated %}
            {% if page_obj.has_previous %}
                <a class="btn btn-outline-info mb-4" href="?page=1">First</a>
                <a class="btn btn-outline-info mb-4" href="?page={{ page_obj.previous_page_number }}">Previous</a>
            {% endif %}

            {% for num in page_obj.paginator.page_range %}
                {% if page_obj.number == num %}
                    <a class="btn btn-info mb-4" href="?page={{ num }}">{{ num }}</a>
                {% elif num > page_obj.number|add:'-3' and num < page_obj.number|add:'3' %}
                    <a class="btn btn-outline-info mb-4" href="?page={{ num }}">{{ num }}</a>
                {% endif %}
            {% endfor %}

            {% if page_obj.has_next %}
                <a class="btn btn-outline-info mb-4" href="?page={{ page_obj.next_page_number }}">Next</a>
                <a class="btn btn-outline-info mb-4" href="?page={{ page_obj.paginator.num_pages }}">Last</a>
            {% endif %}
        {% endif %}
{% endblock content %}
```

**blog/templates/blog/post_detail.html**

Add routing for user href links
```
{% extends "blog/base.html" %}
{% block content %}
    <article class="media content-section">
        <img class="rounded-circle article-img" src="{{ object.author.profile.image.url }}">
        <div class="media-body">
            <div class="article-metadata">
                <a class="mr-2" href="{% url 'user-posts' object.author.username %}">{{ object.author }}</a>
                <small class="text-muted">{{ object.date_posted }}</small>
                {% if object.author == user %}
                <div>
                    <a class="btn btn-secondary btn-sm mt-1 mb-1" href="{% url 'post-update' object.id %}">Update</a>
                    <a class="btn btn-danger btn-sm mt-1 mb-1" href="{% url 'post-delete' object.id %}">Delete</a>
                </div>
                {% endif %}
            </div>
            <h2 class="article-title" >{{ object.title }}</h2>
            <p class="article-content">{{ object.content }}</p>
        </div>
    </article>
{% endblock content %}
```
