This is a component that offers one of more widgets (tabbed?) that allow selection of external content resources - that is, images for canvases - but also AV resources.

Out of the box the widgets are:

 - a variant of the [[IIIF Browser]] and will yield Content Resources extracted from external IIIF.
 - a plain "paste URL" that expects an image, or an image service, or a video file (and is clever about interrogating what it finds - potentially _very_ clever, e.g., a YouTube page). 

Later we might have a DLCS tab/widget, which allows you to browse the DLCS and ingest something new there and then.

If the user provides an image service, the Manifest Editor has all it needs to know. The DLCS plugin might immediately return the image service URL (even if it has yet to actually process it).

But if I import an image, the import dialog needs to return a public URL for that image, and possibly an image service:

**It is the import dialog’s responsibility to return an image resource - a content resource as might appear in a Manifest - to the ME, rather than just an image URL. Then the dialog can decide exactly what that looks like, what the image URL is, what the image service is, if one is being provided.**


## Import from Desktop, or IIIF-ising of existing images

We can implement a DLCS plugin here and a [[GitHub Integration]] (that depends on the image being committed to a gh-pages branch, or picked up by some other process); other people can implement plugins for their own integrations. Is the GH plugin the same as the one used for loading and saving Manifests? Yes, but the user just needs to be aware of the gh-pages requirement. And if the image is picked up for transformation, the ME needs to know what it should put into the Manifest. The DLCS plugin has the same responsibility.