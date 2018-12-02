---
layout: post
Title: Remove Title Bar From Gnome3
Date: 2013.04.22
tags: [Linux, Gnome]
---

Only affect the default theme, Adwaita.
----
Open your Adwaita xml
(/usr/share/themes/Adwaita/metacity-1/metacity-theme-3.xml) file and change
the line that configures the maximized window (name=”max”). Add
has_title=”false” to the “frame_geometry” tag. Then look for
“title_vertical_pad”, and change “value=8″ to value=”1″. Restart Gnome 3 and
maximize a window.

Interestingly, you can still drag down from a menu or button bar to return to
a normal sized window and get your duplicate titlebar back.

something like this
<frame_geometry name="max" has_title="false" title_scale="medium" parent="normal" rounded_top_left="false" rounded_top_right="false">
         <distance name="left_width" value="0" />
         <distance name="right_width" value="0" />
         <distance name="left_titlebar_edge" value="0"/>
         <distance name="right_titlebar_edge" value="0"/>
         <distance name="title_vertical_pad" value="0"/>
         <border name="title_border" left="0" right="0" top="0" bottom="0"/>
         <border name="button_border" left="0" right="0" top="0" bottom="0"/>
         <distance name="bottom_height" value="0" />
</frame_geometry>

How to restart Gnome quickly
---
Restart the shell by pressing `ALT + F2`, then `r`, then `Enter`


Reference
http://www.webupd8.org/2011/05/how-to-remove-maximized-windows.html
https://wiki.archlinux.org/index.php/GNOME_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)
https://wiki.archlinux.org/index.php/GNOME#Restarting_the_shell
