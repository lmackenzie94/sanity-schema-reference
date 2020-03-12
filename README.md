# [Sanity.io](https://sanity.io) Schema Reference

### slug
``` javascript
{
    name: 'slug',
    title: 'Slug',
    type: 'slug',
    options: {
        source: 'name',
        maxLength: 96
    }
},
```

### image
``` javascript
{
    name: 'image',
    title: 'Image',
    type: 'image',
    options: {
        hotspot: true
    }
},
```

### block content
``` javascript
{
    name: 'bio',
    title: 'Bio',
    type: 'array',
    of: [
    {
        title: 'Block',
        type: 'block',
        styles: [{title: 'Normal', value: 'normal'}],
        lists: []
    }
    ]
}
```

### reference
``` javascript
{
    name: 'author',
    title: 'Author',
    type: 'reference',
    to: { type: 'author' }
},
```

### reference array
``` javascript
{
    name: 'categories',
    title: 'Categories',
    type: 'array',
    of: [{ type: 'reference', to: { type: 'category' } }]
},
```

### validation (required)
``` javascript
{
    name: 'alt',
    title: 'Alternative Text',
    description: 'Important for SEO and accessibility.',
    type: 'string',
    validation: Rule =>
    Rule.error(
        `Alt text is requires as it is important for SEO and accessibility`
    ).required(),
    options: {
        // takes this field out from behind an 'Edit' button
        isHighlighted: true
    }
},
```

### validation (unique)
``` javascript
{
    name: 'authors',
    title: 'Author(s)',
    type: 'array',
    validation: Rule =>
    Rule.unique().error(`You can't list the same author more than once.`),
    of: [
    {
        type: 'authorReference'
    }
    ]
},
```

### tags
``` javascript
{
    name: 'keywords',
    title: 'Keywords',
    type: 'array',
    of: [{ type: 'string' }],
    options: {
        layout: 'tags'
    }
},
```

### list
``` javascript
{
    name: 'platform',
    title: 'Platform',
    type: 'string',
    options: {
    list: [
        { value: 'Facebook', title: 'Facebook' },
        { value: 'Twitter', title: 'Twitter' },
        { value: 'Youtube', title: 'Youtube' }
    ]
    }
},
```

### fieldsets / icon example
``` javascript
import { FiUser } from 'react-icons/fa';

export default {
  name: 'author',
  title: 'Author',
  type: 'document',
  icon: FiUser,
  fieldsets: [
    {
      name: 'social',
      title: 'Social Media',
      options: { collapsible: true, collapsed: false }
    }
  ],
   fields: [
    {
      name: 'twitter',
      title: 'Twitter',
      type: 'string',
      fieldset: 'social'
    },
    {
      name: 'github',
      title: 'Github',
      type: 'string',
      fieldset: 'social'
    }
  ],
}
```

### dynamic preview (ex. 1)
``` javascript
import React from 'react';
import {
  faFacebookF,
  faTwitter,
  faYoutube
} from '@fortawesome/free-brands-svg-icons';
import { FontAwesomeIcon } from '@fortawesome/react-fontawesome';

const getSocialIcon = platform => {
  switch (platform) {
    case 'Facebook':
      return faFacebookF;
    case 'Twitter':
      return faTwitter;
    case 'Youtube':
      return faYoutube;
    default:
      break;
  }
};
{
    preview: {
        select: {
            title: 'platform',
            subtitle: 'url.en'
        },
        prepare(selection) {
            const { title, subtitle } = selection;
            return {
                title,
                subtitle,
                media: (
                    <div>
                        <FontAwesomeIcon icon={getSocialIcon(title)} />
                    </div>
                )``
            };
        }
    }
}
```

### dynamic preview (ex. 2)
``` javascript
fields: [
    {
      name: 'text',
      title: 'Text',
      type: 'localeString'
    }
  ],
preview: {
    select: {
        ['English Text']: 'text.en',
        ['French Text']: 'text.fr',
        ['Chinese Text']: 'text.cn'
    },
    prepare(selection) {
        let missingTranslations = [];
        for (let item in selection) {
        if (!selection[item]) {
            missingTranslations.push(item);
        }
        }
        return {
        title: selection['English Text'],
        subtitle:
            missingTranslations.length > 0
            ? `MISSING: ${missingTranslations.join(', ')}`
            : null
        };
    }
}
```

### localized string
``` javascript
const supportedLanguages = [
  { id: 'en', title: 'English', isDefault: true },
  { id: 'fr', title: 'French' },
  { id: 'cn', title: 'Chinese' }
];

export default {
  name: 'localeString',
  title: 'Text',
  type: 'object',
  fieldsets: [
    {
      title: 'Translations',
      name: 'translations',
      options: { collapsible: true }
    }
  ],
  fields: supportedLanguages.map(lang => ({
    title: lang.title,
    name: lang.id,
    type: 'string',
    fieldset: lang.isDefault ? null : 'translations'
  }))
};
```