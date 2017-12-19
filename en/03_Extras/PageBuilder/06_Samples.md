Examples of configuration can be found <a href="https://github.com/mnoskov/pagebuilder/tree/master/assets/plugins/pagebuilder/config" target="_blank">here</a>. (For example blocks to become available for selection, you need to rename files `*.php.sample` to `*.php`)

### Example 1

In this example, a content block with a single-line and multi-line text fields is created, which will be displayed in templates with identifier "2" under the name "Example 1":

```
<?php
    return [
        'title' => 'Example 1',
        'show_in_templates' => 2,
        'templates' => [
            'owner' => '
                <div>Text:<br> [+text+]</div>
                <div>Textarea:<br> [+textarea+]</div>
            ',
        ],
        'fields' => [
            'text' => [
                'caption' => 'Text',
                'type'    => 'text',
            ],
            'textarea' => [
                'caption' => 'Textarea',
                'type'    => 'textarea',
                'default' => 'Default content for textarea',
                'height'  => '80px',
            ],
        ],
    ];
```

### Example 2

In the following example, the content block will contain a single-line header field, a date and a list of images.
The main output template will be loaded from the chunk named "example2".

The block will be displayed only in documents with identifiers "37" and "41" under the name "Example 2":
```
<?php
    return [
        'title' => 'Example 2',
        'show_in_docs' => [ 37, 41 ],
        'templates' => [
            'owner'  => '@CHUNK example2',
            'images' => '<img src="[+image+]">',
        ],
        'fields' => [
            'text' => [
                'caption' => 'Text',
                'type'    => 'text',
            ],
            'date' => [
                'caption' => 'Date',
                'type'    => 'date',
            ],
            'images' => [
                'caption' => 'Images',
                'type'    => 'group',
                'fields'  => [
                    'image' => [
                        'caption' => 'Image',
                        'type'    => 'image',
                    ],
                ],
            ],
        ],
    ];
```
