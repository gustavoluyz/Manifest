One of the options supported for [[Saving IIIF]] is to save to IIIF resources.

This feature is probably not in v1, but we can provide an implementation of a client in Manifest Editor later, and a reference server implementation (e.g., a container over an S3 bucket, or file system).

The user browses around IIIF resources with a [[IIIF Browser]] (as in Load, Import). These resources are IIIF Collections and Manifests. Not all resources will support all operations, and adopters might have a mixture of read-only and "writable" collections. For UI purposes, the adopter can use labels to convey this sort of information to the client, but as well as that, the client can issue an OPTIONS request to see whether a resource (or potential resource) supports a particular operation:

```
OPTIONS /some-top-level/my-collection HTTP/1.1

HTTP/1.1 204 No Content
Allow: OPTIONS, GET, HEAD, POST
```

## Save to a Collection

The client POSTs a Manifest to a IIIF Collection URL.
The client can indicate the required URL of the manifest in its `id` property, but the server can override that and store the manifest with a different ID from that supplied. This also means that the client doesn't have to know in advance what the Manifest URL will be, it allows the server to assign `id`s to manifests.

The relationship between `id` in the POSTed manifest and on the server is an implementation decision, the protocol is the same either way, making this flexible.

The server returns a 201 Created if the operation resulted in a new resource being created, or a 204 if the operation updated an existing resource (which might be the case if the `id` of the manifest is that of one already in the collection - again, an implementation decision.

Either way, for 201 and 204, the server returns a `Location` header with the `id` of the now dereferenceable manifest, so that the editor can carry on with that URL.

It's up to the server _where_ in the Collection `items` the Manifest ends up (e.g., first, last, alphabetical). This is an implementation decision.

## Save a Collection or Manifest

A Collection can be saved to another collection with POST, as above - again, it's up to the server what to make of the POSTed collection, whether to assign it a new `id`.

A Collection itself, or a Manifest, can be saved directly with PUT to a URL.

This results in a 201 Create or a 204 for an update. It can also be rejected for various reasons.


## Add a reference to a Manifest to a Collection

In some scenarios you will already have a saved Manifest somewhere - you are not asking the server to store the manifest, just to add a reference to it to the Collection's `items`.

This works in exactly the same way as the POST to a collection - except that the POSTed Manifest body does not have an `items` property.

It could be as simple as `{ "id": xxx, "type": "Manifest" }` (like a Vault reference).
Or it could include a `label`, and possibly other metadata - but not `items`, which indicates you want to store the actual Manifest.

It is an implementation decision how the server handles it. For example, POSTing just the bare manifest reference might cause the server to GET the Manifest to find out what its `label` is, or assign its own label. Similarly the semantics of supplying a `label` at POST time are up to the implementation. Typically this would be "use this label _in the collection_ even if different from the Manifest's label when dereferenced" - but it's up to the server.
