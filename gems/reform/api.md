---
layout: reform
title: "Reform: API"
---

# Reform: API

## Disposable API

Every Reform form object inherits from `Disposable::Twin`, making every form a twin and giving each form the entire twin API such as.

* Defaults using `:default`.
* Coercion using `:type` and `:nilify`.
* Nesting
* Composition
* Hash fields

If you're looking for a specific feature, make sure to check the [Disposable documentation](/gems/disposable/api.html)


## Inheritance

Forms can be derived from other forms and will inherit all properties and validations.

    class AlbumForm < Reform::Form
      property :title

      collection :songs do
        property :title

        validates :title, presence: true
      end
    end

Now, a simple inheritance can add fields.

    class CompilationForm < AlbumForm
      property :composers do
        property :name
      end
    end

This will _add_ `composers` to the existing fields.

You can also partially override fields using `:inherit`.

    class CompilationForm < AlbumForm
      property :songs, inherit: true do
        property :band_id
        validates :band_id, presence: true
      end
    end

Using `inherit:` here will extend the existing `songs` form with the `band_id` field. Note that this simply uses [representable's inheritance mechanism](https://github.com/apotonick/representable/#partly-overriding-properties).

## Overriding Accessors

You're free to override form accessors for presentation and coercion.

    class SongForm < Reform::Form
      property :title

      def title
        super.capitalize
      end
    end

As always, use `super` for the original method.

## Forms In Modules

To maximize reusability, you can also define forms in modules and include them in other modules or classes.

    module SongsForm
      include Reform::Form::Module

      collection :songs do
        property :title
        validates :title, presence: true
      end
    end

This can now be included into a real form.

    class AlbumForm < Reform::Form
      property :title

      include SongsForm
    end

Note that you can also override properties [using inheritance](#inheritance) in Reform.

When using coercion, make sure the including form already contains the `Coercion` module.

If you want to provide accessors in the module, you have to define them in the `InstanceMethods` module.

    module SongForm
      include Reform::Form::Module

      property :title

      module InstanceMethods
        def title=(v)
          super(v.trim)
        end
      end
    end

This is important so Reform can add your accessors after defining the default ones.

# Injecting Objects

safe args can be passed in constructor
