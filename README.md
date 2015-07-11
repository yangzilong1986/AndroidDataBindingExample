##Android Data Binding 代码实战
本代码用来练习Android Data Binding技术，
目前实现
- 豆瓣电影搜索
  利用Data Binding在RecycleView展示电影列表

  ![data-binding-example](http://image.aswifter.com/data-binding-example.png)

###Model

```java
public class Movie extends BaseObservable {
    private String id;
    private String title;
    private String original_title;
    private String year;
    private Images images;
    private Rating rating;

    @Bindable
    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
        notifyPropertyChanged(BR.title);

    }

    @Bindable
    public String getDescription() {
       return this.original_title + "\n" + this.getYear();
    }

    public void setOriginal_title(String original_title) {
        this.original_title = original_title;
        notifyPropertyChanged(BR.description);
    }

    public void setYear(String year) {
        this.year = year;
        notifyPropertyChanged(BR.description);
    }

    ...
   }
```

layout绑定时，实际使用的是get方法，所以我定义了一个getDescription方法，
在setYear和setOriginal_title方法中通知description更新
```java
notifyPropertyChanged(BR.description);
```

###Layout

```xml
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <data>
        <variable
            name="movie"
            type="com.aswifter.databinding.model.Movie" />
    </data>

    ...

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:orientation="vertical">

        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="@{movie.title}"
            android:textAppearance="@style/TextAppearance.AppCompat.Title"
            android:textColor="@color/primary_text" />

        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginTop="2dp"
            android:text="@{movie.description}"
            android:textColor="@color/secondary_text" />


        <RatingBar
            style="@android:style/Widget.DeviceDefault.Light.RatingBar.Small"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="10dp"
            android:numStars="10"
            android:rating="@{movie.rating.average}"
            android:stepSize="0.5" />
    </LinearLayout>

   ...

```
在Layout中使用@{movie.rating.average}这样的表达式进行绑定


###Binding
```java
Movie movie = mMovies.get(position);
Glide.with(MovieActitiviy.this)
        .load(movie.getImages().getMedium())
        .fitCenter()
        .into(holder.binding.ivMovie);
holder.binding.setVariable(com.aswifter.databinding.BR.movie, movie);
holder.binding.executePendingBindings();
```

在binding类设置变量的值，网络图片在xml里自动显示不了，
可以通过binding获取ImageView，再调用Glide加载图片。