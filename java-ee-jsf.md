# JSF

## Templating

### Static templates

There are three types of files:
- *Template*
  * Contains <ui:insert> with a *name* attribute, which is a placeholder
  * The <ui:insert> can contain a nested <ui:include> that serves as the default replacement if there is no <ui:define> with this name in the *Application* file
- *Composition*
  * Contains <ui:composition> and holds content that is injected into the template
  * Everything outside the <ui:composition> tag is removed when included into the template
- *Application*
  * Is the application of a configured template
  * Contains a <ui:composition> together with *template*="someTemplate.xhtml" to reference a *Template*
  * Inside <ui:composition> one can define <ui:insert> with a *name* attribute to replace the <ui:include> of the same name in the *Template* file

### References
- https://www.mkyong.com/jsf2/jsf-2-templating-with-facelets-example/
