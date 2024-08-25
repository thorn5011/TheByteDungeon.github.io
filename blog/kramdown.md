# 1. Kramdown

- [1. Kramdown](#1-kramdown)
- [2. Code](#2-code)
- [3. Lists](#3-lists)
- [4. Tables](#4-tables)
- [5. Links](#5-links)
- [6. Images](#6-images)
- [7. Expand sections](#7-expand-sections)


<https://kramdown.gettalong.org/syntax.html>


# 2. Code

This is a code block

    code goes brrrr
    rrrrrrrrrrrrrr
^
    rrrrrrrrrrrrr
    rrrrrrrrrrrrr


~~~python
more code in here with "~"
import python
~~~

---

# 3. Lists

- first
* second
- third

1. asd
1. asd
1. asd

***

# 4. Tables

| first|second|third|
|----|:-----|------:|
| first|second|third|
| first|second|third|
|--------
| first|second|third|
| what is this
|===
| hello

# 5. Links

Just use brackets <https://vg.no>

[_config.yml](../_config.yml)


# 6. Images

![smiley](../images/fine.png){:height="36px" width="36px"}

![smiley](../images/fine.png){:height="100px" width="100px"}


# 7. Expand sections

<details>
<summary>Click pls</summary>

Hello there :smile:

</details>

<details>
<summary>Code block</summary>

```sh
ls -l
```
</details>


<details markdown="1">
<summary>Code block with markdown setting</summary>

```sh
ls -l
```
</details>


<button type="button" class="collapsible">Open Collapsible</button>
<div class="content">
    <p>Lorem ipsum...</p>
    <p>
    ```sh
    ls -l
    ```
    <pre><code>this is code</pre></code>
    </p>
</div>
