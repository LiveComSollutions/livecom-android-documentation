## Style customization

SDK allows to customize some values. They are:

1.  Fonts. Three custom fonts can be provided - Regular, Bold and Semi-Bold.
2.  Some corners radius. There are two dimensions that most UI elements use - "small" and "big" corner radius.
3.  Some colors. Gradient start-end colors, brand colors. Both light and dark themes could be provided.

So how to change this properties? You need to create resources in your application xml files named like in table below:

| Parameter | Properties name | Default value |
| --- | --- | --- |
| Color | shoppablevideo\_brand\_primary\_fill\_alpha15 | #260091FF - light and night |
| Color | shoppablevideo\_brand\_primary | #0091FF - light and night |
| Color | shoppablevideo\_brand\_secondary | #EF5DA8 - light and night |
| Color | shoppablevideo\_gradient\_start\_color | #FF0091FF - light and night |
| Color | shoppablevideo\_gradient\_end\_color | #FF00D1FF - light and night |
| Dimension | shoppablevideo\_buttons\_small\_corner\_radius | 10dp |
| Dimension | shoppablevideo\_buttons\_big\_corner\_radius | 15dp |
| Font | shoppablevideo\_bold\_font.ttf | [https://fonts.google.com/specimen/Inter?query=inter](https://fonts.google.com/specimen/Inter?query=inter) |
| Font | shoppablevideo\_regular\_font.ttf | [https://fonts.google.com/specimen/Inter?query=inter](https://fonts.google.com/specimen/Inter?query=inter) |
| Font | shoppablevideo\_semi\_bold\_font.ttf | [https://fonts.google.com/specimen/Inter?query=inter](https://fonts.google.com/specimen/Inter?query=inter) |

For example, to override color `shoppablevideo_brand_primary` you can add resource to your xml file where you store colors:

```plaintext
<?xml version="1.0" encoding="utf-8"?>
<resources>
   <color name="shoppablevideo_brand_primary">#0091FF</color>
</resources>
```

If you want to override fonts - create font resource folder and put your `ttf` file there with appropriate name (look at the table above):   
![fonts folder location](https://github.com/LiveComSollutions/android-sdk-documentation/blob/main/fonts%20folder%20location.png?raw=true)
