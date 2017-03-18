---
layout: post
title: Bindings de texto usando knockout con jquerymobile
date: '2012-09-27 04:54:00'
tags:
- knockout
categories:
- Frontend
---

Cuando usas los bindings de texto de knockout en combinación con jquerymobile puedes encontrarte con algún problemilla …

El otro día me encontré con el problema de que al intentar crear “bindings” de texto usando knockout con ciertos componentes de jquery mobile se me “rompía” la interfaz al cargar la página. Me pasó con los textos de las cabeceras de los collapsibles y con los textos de los botones.

Esto sucede porque jquery mobile añade sus propios elementos y clases al html que estamos generando y por tanto, al generarse ese marcado extra, el texto ya no está en la cabecera (h*) o en el link (a) sobre el que hemos definido el binding, sino que se ha añadido en un <span> con la clase ui-btn-text.

Para arreglar esto, lo que finalmente hice es crear un custom binding de texto muy simple que lo único que hace es esto

```javascript
ko.bindingHandlers.jqmtext = {
  update: function(element, valueAccessor, allBindingsAccessor, viewModel, bindingContext) {
    var value = valueAccessor();
    var valueUnwrapped = ko.utils.unwrapObservable(value);
    $(element).find(".ui-btn-text").text(valueUnwrapped);
  }
};
```

Una vez que tengo el binding definido, ya puedo utilizarlo en mi código html sin problemas y mi interfaz dejará de “romperse” …

```html
//Collapsibles
<div data-role="collapsible">
  <h2 data-bind="jqmtext : mitexto"></h2>
  ....
</div>

//Buttons
<a href="#" data-role="button" data-bind="jqmtext : mitexto"></a>
```


Hasta pronto!!

