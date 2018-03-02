# HOW OUR PRODUCT CUSTOMIZATION ENGINE WORKS

[https://tech.holmescustom.com/2018/01/26/how-our-product-customization-engine-works/](https://tech.holmescustom.com/2018/01/26/how-our-product-customization-engine-works/)

I’m Cory Long, a software developer at Holmes Custom. In my three years with this company, we’ve rolled out some truly cutting edge technology. We’ve moved our websites to the extendable Magento platform and have developed countless features for customers and staff alike. Today, I’m going to talk about what I think is the most impressive technology we’ve developed:

## OUR PRODUCT CUSTOMIZATION ENGINE

Our Product Customization Engine (Also called PCE and sometimes The Design Wizard) is a powerful tool we built to allow customers total control of their designs. It includes a vast number of customization options for text, images, cliparts, shapes, and even the product itself. It gives the customer an accurate preview, so they know exactly what to expect. Finally, most designs can go straight from website to production, creating less work for us and allowing us to compete on price.

## WHEN WE SET OUT TO BUILD THIS, WE HAD A FEW REQUIREMENTS

It had to respond quickly. Many existing wizards generated the preview on the server, then passed an image back down. This can mean a wait of 1 or 2 seconds for each and every change. This was too long for our customers.

It needed to produce something we could send straight to production. At the time, there was a lot of human work involved in each design, aligning and resizing text and images. Often, we had to reach out to customers for potential issues, such as text being too long to fit onto a design.

It needed to be able to provide complete and total control over the design. We wanted customers to be able to create anything they could think up, and not feel limited by the tools. This was our key to standing out from the competition.

Enter the HTML5 Canvas element. It is an HTML element that renders graphics using a JavaScript API. This technology has been used in everything from Google Doodles to 3D web games to our very own customization engine.

## THE PROCESS FROM YOUR IDEA TO YOUR PRODUCT

First, designs are loaded onto the HTML Canvas elment using a JSON file. It allows us to define the size of the product, the options, populate a starting template, and even import a customers previous design.

When the customer is done customizing their design and clicks Add To Cart, it exports the design to SVG format. SVG is a vector image format. Raster image formats such as PNG and JPG define where and what color each pixel is. Vector is more like an instruction set. It will say where to draw a line, what color it should be, and more. Additionally, we create a cutline on the border of the design that we later use to tell the machines where to cut the material. Finally, it exports the state of the Canvas as a JSON, so the customer can load it back in if they need to update it or reorder.

We use the Cairo graphics library to convert the SVG to PDF. The PDF format is perfect for printing. We also convert the SVG to PNG format, so we can provide a preview of the product in the customers cart.

The PDF then gets loaded into CorelDraw, our graphics editor software. It is kind of like Adobe Photoshop, except CorelDraw is geared towards printing rather than web. CorelDraw then sends the file directly to any one of the wide range of machines we use to print or create our products. From there, the product will be out of our campus in typically less than 24 hours and on it’s way to you.

## CHALLENGES

### FONTS

One of the requirements was providing support for dozens of fonts. Every font had to work in +98% browsers, on the Server, and on CorelDraw. We had to support Braille, while restricting the effects to be applied to it to stay ADA compliant. We also had to support Barcode, which is only valid if the text starts and ends with an asterisk. We decided to use the WOFF font format. It’s a modern font format we found gave the greatest browser support, had great support for Braille and Barcode, and worked well during the SVG to PDF conversion. Each font goes through careful automated and manual testing to ensure it works for customers and staff.

### CURVED TEXT

Most of our text manipulation needs were provided by our Fabric.JS library. Curved text was not supported, so we extended Fabric.JS to support curved text with the help of some tricky geometric math. We then had to provide options to allow customers to finely tune their curved text, such as radius, spacing, and type of arc. Finally, when the canvas gets exported to SVG, each letter gets positioned individually to ensure it gets converted correctly through each step.

### MONOCHROME

Many products only support certain colors, such Engraved Name Tags, which are one color on the surface, and is engraved to reveal another color. Images need to be monochromed to be part of the design. In the past, customers would provide images and staff would manually decide which shades would become the primary or secondary color. We used the ImageMagick library to create a tool for customers to not only monochrome images themselves, but set the threshold to create the desired effect.

## IN REVIEW
These are only scratching the surface of all the incredible features we packed into this powerful tool. We are continually improving our Product Customization Engine and rolling it out to more of our products. Technology is one of several ways we stand out from our competition. Feel free to explore the many features of our Product Customization Engine yourself!
