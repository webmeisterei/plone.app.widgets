======================================
Using mockup patterns in Plone's forms
======================================

In this tutorial we will learn how we can configure and use patterns
developed in Mockup project in Plone's forms.

Passing options in custom widget
================================

Lets first remember how we apply custom widget to a field and pass custom
parameters for widget.

One way is to get this done using `plone.autoform`_:

.. code:: python

    class ISomeSchema(model.Schema):

        form.widget('somefield', SomeWidget, customparam='customparamvalue')
        somefield = zope.schema.TextField(
            title=u'Some field',
            description=u'Some field description',
            )

But sometimes we already have schema defined and we want only to override
specific field:

.. code:: python

    @adapter(getSpecification(ISomeForSchema['somefield']), ICustomFormLayer)
    @implementer(z3c.form.interfaces.IFieldWidget)
    def SubjectsFieldWidget(field, request):
        widget = z3c.form.widget.FieldWidget(field, SomeWidget(request))
        widget.customparam = 'customparamvalue'
        return widget

We wont go into details about how dexterity (z3c.form) deals with widget, but
above snippets are enough to refresh what we already know.


Your first mockup pattern
=========================

Now that we know the basics of creating forms we can look at how we can
use Mockup patterns.

.. note::

    In Plone 4.3 make sure that `plone.app.widgets`_ is installed. This
    will ensure that needed javascript will be present in browser.

In this example we will use general MockupWidget which expects two paramentes:
``pattern`` and ``pattern_options``. We will configure MockupWidget in a way
that will show date picker with default value 10 years in future and custom
placeholder.

.. code::python

    today = datetime.date()

    class ISomeSchema(model.Schema):

        form.widget('somefield', SomeWidget,
            type='input',
            pattern='date',
            pattern_options=dict(
                time: False,
                placeholderDate: 'Travel to future ...',
            ))
        somefield = zope.schema.TextField(
            title=u'Some field',
            description=u'Some field description',
            default=date(today.year+10, today.month, today.day),
            )

Above code will generate the following html:

.. code:: html

    <input type="text"
           value="2024-23-12"
           class="pat-pickadate"
           data-pat-pickadate='{ "time": false,
                                 "placeholderDate": "Travel to future"
                               }'
           />

Above html attributes will correctly invoke `pickadate pattern`_ from mockup
to show `pickadate`_ calendar to make selection of date easier.

As you can see `MockupWidget`_ gives you all the possibilities to use any
pattern from `Mockup`_ and pass options for that pattern to customize its
functionality.

Since its would be quite cumberstone to use `MockupWidget`_ and provide all
options each time, we created few helper widgets which should make using any
mockup widget as easy as before. In previous example we could use
`DateWidget`_ which extends `MockupWidget`_ and sets sensible defaults.

.. code:: python

    form.widget('somefield', DateWidget,
        pattern_options=dict(
            placeholderDate: 'Travel to future ...',
        ))


You can check list of existing widget which we created, but in case of custom
pattern for which we didn't create helper Widget you can use general
`MockupWidget`_.
