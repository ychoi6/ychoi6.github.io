---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
author: Yoonmi Choi
# cover: /assets/image/UNL_Name_RGB_2023.svg
---

<html>
    <head>
    <style>
    
    body {
        position: relative;
        margin: 0;
        height: 100vh;
        background-image: url('{{ site.baseurl }}/assets/image/R-UNL_HEX.svg');
        background-repeat: no-repeat;
        background-attachment: fixed;  
        background-size: 50%; /* Adjust this to your preferred size */
        background-position: center 40%; /* Centers the background image */
    }
    body::before {
        content: "";
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        background: rgba(255, 255, 255, 0.95); /* Adjust this color and opacity */
        z-index: -1;
    }
    </style>
    </head>

    </html>

### About Me

Hello!  
<p>I’m Yoonmi Choi, a PhD student in the Biological Systems Engineering department at the University of Nebraska-Lincoln, where I am part of Songlab. </p>

<p> My research focuses on the intricate interactions between plants and microbes.I use computational modeling and experimental approaches to better understand and optimize these interactions for sustainable agriculture. </p>

<i> This website serves as a platform to document and share my research progress, insights, and beyond. </i>

Feel free to contact me via email at [ychoi6@huskers.unl.edu](mailto:ychoi6@huskers.unl.edu) or explore our lab’s work at [Songlab website](https://cms.unl.edu/engineering/song-lab/).

Thank you for visiting my website!