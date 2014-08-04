---
layout: post
title:  "Welcome to Jekyll! this post name is super long because I might do silly stuff like that"
date:   2014-08-03 06:24:09
permalink: /hello.html
---

You'll find this post in your `_posts` directory - edit this post and re-build (or run with the `-w` switch) to see your changes!
To add new posts, simply add a file in the `_posts` directory that follows the convention: YYYY-MM-DD-name-of-post.ext.

Jekyll also offers powerful support for code snippets:

# this

foobar
You'll find this post in your `_posts` directory - edit this post and re-build (or run with the `-w` switch) to see your changes!

## is

You'll find this post in your `_posts` directory - edit this post and re-build (or run with the `-w` switch) to see your changes!
To add new posts, simply add a file in the `_posts` directory that follows the convention: YYYY-MM-DD-name-of-post.ext.

### a

You'll find this post in your `_posts` directory - edit this post and re-build (or run with the `-w` switch) to see your changes!
To add new posts, simply add a file in the `_posts` directory that follows the convention: YYYY-MM-DD-name-of-post.ext.

#### damn

You'll find this post in your `_posts` directory - edit this post and re-build (or run with the `-w` switch) to see your changes!
To add new posts, simply add a file in the `_posts` directory that follows the convention: YYYY-MM-DD-name-of-post.ext.

##### test

You'll find this post in your `_posts` directory - edit this post and re-build (or run with the `-w` switch) to see your changes!
To add new posts, simply add a file in the `_posts` directory that follows the convention: YYYY-MM-DD-name-of-post.ext.

###### woo

You'll find this post in your `_posts` directory - edit this post and re-build (or run with the `-w` switch) to see your changes!
To add new posts, simply add a file in the `_posts` directory that follows the convention: YYYY-MM-DD-name-of-post.ext.

{% highlight javascript %}
exports.create = function (opts) {
  if (!opts || opts === undefined || opts.name === undefined) 
    return console.log('Please supply a name for this scene!')
  if (exports[opts.name] !== undefined) 
    return console.log('Please select a different name for scene ' + opts.name)

  opts.view = opts.view !== undefined ? opts.view : { pos: [0,0,0], rot: [0,0,0] }
  opts.view.pos = opts.view.pos !== undefined ? opts.view.pos : [0,0,0]
  opts.view.rot = opts.view.rot !== undefined ? opts.view.rot : [0,0,0]
  opts.view.mat = updateView(opts.view) 

  opts.world = opts.world !== undefined ? opts.world : { pos: [0,0,0], rot: [0,0,0]}
  opts.world.pos = opts.world.pos !== undefined ? opts.world.pos : [0,0,0]
  opts.world.rot = opts.world.rot !== undefined ? opts.world.rot : [0,0,0]
  opts.world.mat = updateWorld(opts.world)

  exports[opts.name] = {
      // game entities
      view: opts.view
    , world: opts.world

      // used by internal default draw to cache/check changes in matrix state
    , _view: opts.view
    , _world: opts.world
    , _global: Matrix.multiply(opts.world.mat, opts.view.mat)

      // array of 3d and 2d polys to render
      // only automatically used in default draw function
      // warning: does not do culling, only put things that you plan to 
      // draw into the entities array
    , entities: opts.entities !== undefined ? opts.entities : []

      // game loop event functions
    , update: opts.update !== undefined ? opts.update : function () {}
    , draw: opts.draw !== undefined ? opts.draw : function (context) {

        // check if view or world matrix has changed, if so update global matrix
        if (this._view !== this.view || this._world !== this.world) {
          this.view.mat = this._view === this.view ? this.view : updateView(this.view)
          this._view = this.view
          this.world.mat = this._world === this.world ? this.world : updateWorld(this.world)
          this._world = this.world
          this._global = Matrix.multiply(this.world.mat, this.view.mat) 
        }

        this._width = context.canvas.width / 2
        this._height = context.canvas.height / 2

        // render all entities.

        this.entities.map(function (entity) {
          
          context.font = entity.font !== undefined ? entity.font : this.font
          context.fillStyle = entity.fill !== undefined ? entity.fill : this.fill
          context.strokeStyle = entity.stroke !== undefined ? entity.stroke : this.stroke

          if (entity.verts !== undefined) {

            context.beginPath()

            // entity is already 2d poly, draw like normal poly
            if (entity.verts[0].length === 2) {
              context.moveTo(entity.verts[0][0], entity.verts[0][1])
              entity.verts.map(function (vertex) { context.lineTo(vertex[0],vertex[1]) })
            }

            // entity must be projected to 2d
            else if (entity.verts[0].length === 3) entity.verts.map(function (vertex, index) {

              // product of global matrix
              vertex = Matrix.multiply([[vertex[0],vertex[1],vertex[2],1]], this._global)

              // simple depth (divide depth into x/y) manip, then center elem on view
              vertex[0][0] = (vertex[0][0] / vertex[0][2]) * this._width + this._width
              vertex[0][1] = (vertex[0][1] / vertex[0][2]) * this._height + this._height

              // if this is the first vertex, move context drawing to current position
              if (index === 0) context.moveTo(vertex[0][0], vertex[0][1])

              // move polydraw to next vertex position
              context.lineTo(vertex[0][0], vertex[0][1])

            })

            context.closePath()

          } else if (entity.text !== undefined) {
            // put the text on [0,0] if we're not told where to put it
            entity.pos = entity.pos !== undefined ? entity.pos : [0,0]
            context.fillText(entity.text, entity.pos[0], entity.pos[1], entity.max)
          } else { return }

          if (entity.fill !== undefined || this.fill !== undefined) context.fill()
          if (entity.stroke !== undefined || this.stroke !== undefined) context.stroke()
        }, this)
      }

    , fill: opts.fill // warning! will set auto fill for ALL entities
    , stroke: opts.stroke // ditto!
    , font: opts.font !== undefined ? opts.font : '12px serif'

      // keyboard event functions
    , enter: opts.enter !== undefined ? opts.enter : function () {}
    , esc: opts.esc !== undefined ? opts.esc : function () {}
    , up: opts.up !== undefined ? opts.up : function () {}
    , down: opts.down !== undefined ? opts.down : function () {}
    , left: opts.left !== undefined ? opts.left : function () {}
    , right : opts.right !== undefined ? opts.right : function () {}
  }

  if (opts.data !== undefined)
    for (var data in opts.data)
      if (opts.data.hasOwnProperty(data) && exports[opts.name][data] === undefined)
        exports[opts.name][data] = opts.data[data]
}
{% endhighlight %}

Check out the [Jekyll docs][jekyll] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll's GitHub repo][jekyll-gh].

> This is a blockquote with two paragraphs. Lorem ipsum dolor sit amet,
> consectetuer adipiscing elit. Aliquam hendrerit mi posuere lectus.
> Vestibulum enim wisi, viverra nec, fringilla in, laoreet vitae, risus.
> 
> Donec sit amet nisl. Aliquam semper ipsum sit amet velit. Suspendisse
> id sem consectetuer libero luctus adipiscing.

> This is the first level of quoting.
>
> > This is nested blockquote.
>
> Back to the first level.

> ## This is a header.
> 
> 1.   This is the first list item.
> 2.   This is the second list item.
> 
> Here's some example code:
> 
>     return shell_exec("echo $input | $markdown_script");

*   Red
*   Green
*   Blue

1.  Bird
2.  McHale
3.  Parish

---------------------------------------

*single asterisks*

_single underscores_

**double asterisks**

__double underscores__

![this is a
picture](http://critterbabies.com/wp-content/gallery/kittens/happy-kitten-kittens-5890512-1600-1200.jpg)

[jekyll-gh]: https://github.com/jekyll/jekyll
[jekyll]:    http://jekyllrb.com
