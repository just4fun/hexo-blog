title: The difference between :nth-child and :nth-of-type
date: 2014-12-14 23:15:10
tags: css

---

Just few days ago, when I wanna implement row stripping in a table, I still add `class='odd'` to alternative rows via javascript, and give the color in the css class. It's a little bit sad for a F2E. :(

<!-- more -->

In fact, there are pesudo class selectors in css3 which can easily implement row stripping, and the most useful selectors are `:nth-child` and `:nth-of-type`.

It seems that the two selectors are similar in some situations, but there is exact difference between them. See two examples given in [css-tricks](http://css-tricks.com/the-difference-between-nth-child-and-nth-of-type/) below:

```
<section>
  <p>Little</p>
  <p>Piggy</p>    <!-- Want this one -->
</section>
```

and we use the two selectors:

```
p:nth-child(2) { color: red; }
```
```
p:nth-of-type(2) { color: red; }
```
As we expected, they will do same thing.

Let's see another example:

```
<section>
  <h1>Words</h1>
  <p>Little</p>
  <p>Piggy</p>    <!-- Want this one -->
</section>
```

:nth-of-type will match 'Piggy' element correctly and :nth-child will **NOT** works(it will match the 'Little' element).

Because `p:nth-child(2)` means that, the matched item must be the second child of its parent and what is more, the child must be p element.
However, `p:nth-of-type(2)` just means please select the second p element of its parent.

In other words, `:nth-child` is more conditional.

Besides, there is another situation which I want to use :nth-child and :nth-of-type to implement, but both of them are not fit for it. See a example:

```
<ul>
  <li>odd</li>
  <li></li>
  <li>odd</li>
  <li>
    <ul>
      <li></li>
      <li>odd</li>
      <li></li>
    </ul>
  </li>
</ul>
```
We want to change the color of these 'odd' elements, which will render the parent 'ul' as a row stripping tree, so we set `li:nth-child(odd)` and `li:nth-of-type(odd)` to it.

However, the first two 'odd' will be changed to another color as expected, but the last which in the child 'ul' element will not be changed. The elements beside it will be matched instead.

Because the two pesudo class selectors can only work in same level, which means if there is a child element, the index of the first child of this element will be recounted.

To see actual examples, there is a [:nth Tester](http://css-tricks.com/examples/nth-child-tester/) for you.