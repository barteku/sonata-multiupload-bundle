silasjoisten/sonata-multiupload-bundle
======================================
[![Build Status](https://travis-ci.org/silasjoisten/sonata-multiupload-bundle.svg?branch=master)](https://travis-ci.org/silasjoisten/sonata-multiupload-bundle)
[![Latest Stable Version](https://poser.pugx.org/silasjoisten/sonata-multiupload-bundle/v/stable)](https://packagist.org/packages/silasjoisten/sonata-multiupload-bundle)
[![Total Downloads](https://poser.pugx.org/silasjoisten/sonata-multiupload-bundle/downloads)](https://packagist.org/packages/silasjoisten/sonata-multiupload-bundle)
[![Latest Unstable Version](https://poser.pugx.org/silasjoisten/sonata-multiupload-bundle/v/unstable)](https://packagist.org/packages/silasjoisten/sonata-multiupload-bundle)
[![License](https://poser.pugx.org/silasjoisten/sonata-multiupload-bundle/license)](https://packagist.org/packages/silasjoisten/sonata-multiupload-bundle)
[![codecov](https://codecov.io/gh/silasjoisten/sonata-multiupload-bundle/branch/master/graph/badge.svg)](https://codecov.io/gh/silasjoisten/sonata-multiupload-bundle)

## Installation


### Step 1: Download the Bundle

```console
$ composer require silasjoisten/sonata-multiupload-bundle
```

### Step 2: Enable the Bundle

Then, enable the bundle by adding it to the list of registered bundles
in the `app/AppKernel.php` file of your project:

```php
<?php

class AppKernel extends Kernel
{
    public function registerBundles()
    {
        $bundles = array(
            // ...
            new SilasJoisten\Sonata\MultiUploadBundle\SonataMultiUploadBundle(),
        );

        // ...
    }

    // ...
}
```

If you are using flex register bundle in `config/bundles.php`:
```php 
<?php

return [
    //...
    SilasJoisten\Sonata\MultiUploadBundle\SonataMultiUploadBundle::class => ['all' => true]
];
```

### Step 3: Configuration

First you need to override the default `MediaAdminController.php` set following in your `config/services.yaml`

```yaml
parameters:
    sonata.media.admin.media.controller: SilasJoisten\Sonata\MultiUploadBundle\Controller\MultiUploadController

services:
    #...
```

Now add to the service definition of your Provider and add to tag `multi_upload: true` like:

```yaml

App\Provider\VideoProvider:
    arguments:
        - "App\Provider\VideoProvider"
        # ...
    calls:
        # ...
    tags:
        - { name: sonata.media.provider, multi_upload: true }

```

If you are using default Sonata `MediaProvider` these arguments are already injected.
Otherwise be aware that the provider gets `allowedMimeTypes` and `allowedExtensions` injected.
This arguments will be used by `JavaScript` to validate `MimeType` and `FileExtensions` before the Request starts.

For Example:

```yaml

App\Provider\VideoProvider:
    arguments:
        #- "App\Provider\VideoProvider"
        #- "@sonata.media.filesystem.local"
        #- "@sonata.media.cdn.server"
        #- "@sonata.media.generator.default"
        #- "@sonata.media.video.thumbnail"
        - ['mp4', 'mov', 'flv', 'wmv', 'mxf', 'mkv','m4v','mpg']
        - ['video/mp4', 'video/quicktime', 'video/x-flv','video/x-ms-wmv','application/mxf','video/x-matroska','video/x-m4v','video/mpeg']
        # ...
```

In your provider where you want to use multiupload you need to add following trait:
`SilasJoisten\Sonata\MultiUploadBundle\Traits\MultiUploadTrait`


After That you need to add the dependency to your `config/packages/sonata_admin.yaml`:
```yaml
sonata_admin:
    assets:
        extra_stylesheets:
            - bundles/sonatamultiupload/dist/sonata-multiupload.css

        extra_javascripts:
            - bundles/sonatamultiupload/dist/sonata-multiupload.js
```

#### OPTIONAL

Create `config/packages/sonata_multi_upload.yaml`. You can configure following:

```yaml
sonata_multi_upload:
    max_upload_filesize: 3000000 # 3MB the default value is 0 -> allow every size
```

There is an option `redirect_to` which allows you to redirect after complete upload to your configured page.

```yaml
sonata_multi_upload:
    redirect_to: 'admin_sonata_media_media_list'
```

**HINT:** You can create a controller action in your `MediaAdminController` which is called like 
`createGalleryFromMultiUploadAction`. The MultiUploadBundle passes automatically the id's from the uploaded medias 
to the redirection route for example: `/foo/bar?idx=%5B70%2C71%2C72%5D` so you can take them and create 
a gallery from uploaded medias.

Thats it!

**Notice that the uploader won't work for Providers like: YouTubeProvider, VimeoProvider!**

### 4. Look & Feel

![multiupload](docs/images/multiupload-bundle.gif)

Used Library: 
* [jQuery Upload](https://github.com/danielm/uploader)
