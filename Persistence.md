There is a distinction between persistence of the IIIF JSON people are making (the Manifest, or sometimes the Collection), and the content assets (images, but also videos, audio etc) that are referenced from their IIIF JSON. The Manifest Editor is only editing JSON - text files with links to images, image services etc, but not those images and image services themselves.

This is not obvious to a casual user - if it’s like PowerPoint, can’t I just drag my images in and they become part of the slide deck? We can make the manifest editor work like this - but not on its own! If the addition of content from your own desktop is supported, the manifest editor has to be taking that content and sending it somewhere where it can be hosted (i.e., hosted on the web) and, optionally, transformed (typically, make an image service from the content). 

Many users of the ME do not need this ability, especially inside a museum, library or other place where the assets to be used are already hosted, and often already hosted as IIIF image services. In fact it’s the casual, walkup user who most needs the ability to create new hosted image content, but they are the least likely to be able to provide it!

The method by which this casual user transforms an image on their desktop (or even phone) into a hosted image, or hosted image service, is via a plugin that provides an [[Import of Content Resources]] component for the Shell that is able to trigger hosting and, sometimes, transformation to an image service.

## Save and Save as 

(from Google Doc)