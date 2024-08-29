---
title: Jekyll Markdown
tag: 
layout: post
author: Yoonmi Choi
published: true
categories: modeling
---


Note for homepage build.

#### Link

Add a link to the text:  
`[code](Link)`

Open in a new tab:  
`[code](Link){:target="_blank"}`


#### Text

```
Italics : *Songlab* or _Songlab_  
Bold : **Songlab** or __Songlab__  
Italics+Bold : **UNL _Songlab_**  
Strickthroush : ~~Songlab~~  
```
**Result:**  
Italics : *Songlab* or _Songlab_  
Bold : **Songlab** or __Songlab__  
Italics + Bold : **UNL _Songlab_**  
Strickthroush : ~~Songlab~~  


#### Tips, Warning, and Dangers

Use `{: .block-tip }`.
```
> ##### Tip
>
> This is YC's research notebook.
> Songlab
{: .block-tip }
```
> ##### Tip
>
> This is YC's research notebook.
> Songlab
{: .block-tip }


Use `{: .block-warning}`.
```
> ##### Warning
>
> This is YC's research notebook.  
> Songlab
{: .block-warning }
```
> ##### Warning
>
> This is YC's research notebook.  
> Songlab
{: .block-warning }


Use `{: .block-danger}`.

```
> ##### Danger
>
> This is YC's research notebook.  
> Songlab
{: .block-danger }
```

> ##### Danger
>
> This is YC's research notebook.  
> Songlab
{: .block-danger }