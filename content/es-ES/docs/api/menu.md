## Clase: Menu

> Crea menús de aplicaciones nativas y menús contextuales.

Process: [Main](../glossary.md#main-process)

### `new Menu()`

Crea un nuevo menú.

### Métodos Estáticos

La clase `menú` tiene los siguientes métodos estáticos:

#### `Menu.setApplicationMenu(menu)`

* `menu` Menu | null

Configura el `menu` como el menú de la aplicación en macOS. En Windows y Linux, el `menu` se configurará como menú superior de cada ventana.

Also on Windows and Linux, you can use a `&` in the top-level item name to indicate which letter should get a generated accelerator. For example, using `&File` for the file menu would result in a generated `Alt-F` accelerator that opens the associated menu. The indicated character in the button label gets an underline. The `&` character is not displayed on the button label.

Passing `null` will suppress the default menu. On Windows and Linux, this has the additional effect of removing the menu bar from the window.

**Note:** The default menu will be created automatically if the app does not set one. It contains standard items such as `File`, `Edit`, `View`, `Window` and `Help`.

#### `Menu.getApplicationMenu()`

Devuelve `Menu | null` - El menú de aplicación, si se creó, o `null` en caso contrario.

**Nota:** La instancia devuelta `Menu` no soporta adiciones dinámicas o la eliminación de elementos del menú. [Instance properties](#instance-properties) todavía puede ser modificada dinámicamente.

#### `Menu.sendActionToFirstResponder(action)` *macOS*

* `action` String

Envía la `action` al primer respondedor de la aplicación. Esto es usado para emular los comportamientos del menú macOS por defecto. Normalmente usarías el [`rol`](menu-item.md#roles) propiedad de un [`MenuItem`](menu-item.md).

Consulte la [macOS Cocoa Event Handling Guide](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/EventArchitecture/EventArchitecture.html#//apple_ref/doc/uid/10000060i-CH3-SW7) para más información sobre las acciones nativas de macOS.

#### `Menu.buildFromTemplate(template)`

* `plantilla` (MenuItemConstructorOptions | MenuItem)[]

Devuelve `Menu`

Generalmente, el `template` es un arreglo de `options` para construir un [MenuItem](menu-item.md). El uso puede ser referenciado más arriba.

You can also attach other fields to the element of the `template` and they will become properties of the constructed menu items.

### Métodos de Instancia

El objeto`menu` tiene los siguientes métodos de instancia:

#### `menu.popup(options)`

* `opciones` Objecto (opcional) 
  * `window` [BrowserWindow](browser-window.md) (opcional) - Por defecto es la ventana seleccionada.
  * `x` Number (opcional) - Por defecto es la posición actual del cursor del ratón. Debe declararse si `y` es declarada.
  * `y` Number (opcional) - Por defecto es la posición actual del cursor del ratón. Debe declararse si `y` es declarada.
  * `positioningItem` Number (opcional) *macOS* - El índice del elemento de menú para posicionar por debajo del cursor del ratón en las coordenadas específicas. Por defecto es -1.
  * `callback` Function (opcional) - Llamada cuando se cierra el menu.

Este menú aparece como un menú contextual en el [`BrowserWindow`](browser-window.md).

#### `menu.closePopup([browserWindow])`

* `browserWindow` [BrowserWindow](browser-window.md) (opcional) - Por defecto es la ventana seleccionada.

Cierra el menú de contexto en la `browserWindow`.

#### `menu.append(menuItem)`

* `menuItem` [MenuItem](menu-item.md)

Anexa el `menuItem` al menú.

#### `menu.getMenuItemById(id)`

* `id` Cadena

Devuelve `MenuItem` el item con el `id` especificado

#### `menu.insert(pos, menuItem)`

* `pos` Entero
* `menuItem` [MenuItem](menu-item.md)

Inserta el `menuItem` en la posición `pos` del menú.

### Eventos de Instancia

Los Objetos creados con `new Menu` emiten los siguientes eventos:

**Nota:** Algunos eventos sólo están disponibles en sistemas operativos específicos y se etiquetan como tal.

#### Evento: 'menu-will-show'

Devuelve:

* `event` Event

Emitido cuando se llama a `menu.popup()`.

#### Evento: 'menu-will-close'

Devuelve:

* `event` Event

Se emite cuando una ventana emergente se cierra manualmente o con `menu.closePopup()`.

### Propiedades de Instancia

Los objetos `menu` también tienen las siguientes propiedades:

#### `menu.items`

Un arreglo `MenuItem[]` contiene los elementos del menú.

Cada `Menu` se compone de múltiples [`MenuItem`](menu-item.md) y cada `MenuItem` tiene un submenú.

### Eventos de Instancia

Objetos creados con `new Menu` o retornados por `Menu.buildFromTemplate` emiten los siguientes eventos:

## Ejemplos

La clase `Menu` solo está disponible en el proceso principal, pero también se puede usar en el proceso de renderizado a través del módulo [`remote`](remote.md).

### Proceso principal

Ejemplo de la creación del menú de la aplicación en el proceso principal con la API de plantilla:

```javascript
const { app, Menu } = require('electron')

const template = [
  // { role: 'appMenu' }
  ...(process.platform === 'darwin' ? [{
    label: app.getName(),
    submenu: [
      { role: 'about' },
      { type: 'separator' },
      { role: 'services' },
      { type: 'separator' },
      { role: 'hide' },
      { role: 'hideothers' },
      { role: 'unhide' },
      { type: 'separator' },
      { role: 'quit' }
    ]
  }] : []),
  // { role: 'fileMenu' }
  {
    label: 'Archivo',
    submenu: [
      isMac ? { role: 'close' } : { role: 'quit' }
    ]
  },
  // { role: 'editMenu' }
  {
    label: 'Editar',
    submenu: [
      { role: 'undo' },
      { role: 'redo' },
      { type: 'separator' },
      { role: 'cut' },
      { role: 'copy' },
      { role: 'paste' },
      ...(isMac ? [
        { role: 'pasteAndMatchStyle' },
        { role: 'delete' },
        { role: 'selectAll' },
        { type: 'separator' },
        {
          label: 'Speech',
          submenu: [
            { role: 'startspeaking' },
            { role: 'stopspeaking' }
          ]
        }
      ] : [
        { role: 'delete' },
        { type: 'separator' },
        { role: 'selectAll' }
      ])
    ]
  },
  // { role: 'viewMenu' }
  {
    label: 'View',
    submenu: [
      { role: 'reload' },
      { role: 'forcereload' },
      { role: 'toggledevtools' },
      { type: 'separator' },
      { role: 'resetzoom' },
      { role: 'zoomin' },
      { role: 'zoomout' },
      { type: 'separator' },
      { role: 'togglefullscreen' }
    ]
  },
  // { role: 'windowMenu' }
  {
    label: 'Window',
    submenu: [
      { role: 'minimize' },
      { role: 'zoom' },
      ...(isMac ? [
        { type: 'separator' },
        { role: 'front' },
        { type: 'separator' },
        { role: 'window' }
      ] : [
        { role: 'close' }
      ])
    ]
  },
  {
    role: 'help',
    submenu: [
      {
        label: 'Learn More',
        click () { require('electron').shell.openExternalSync('https://electronjs.org') }
      }
    ]
  }
]

const menu = Menu.buildFromTemplate(template)
Menu.setApplicationMenu(menu)
```

### Proceso de renderizado

A continuación, un ejemplo de cómo crear un menú dinámicamente en una página web (proceso de renderizado) utilizando el módulo [`remote`](remote.md), y mostrarlo cuando el usuario le dé clic derecho a la ventana:

```html
<!-- index.html -->
<script>
const { remote } = require('electron')
const { Menu, MenuItem } = remote

const menu = new Menu()
menu.append(new MenuItem({ label: 'MenuItem1', click() { console.log('item 1 clicked') } }))
menu.append(new MenuItem({ type: 'separator' }))
menu.append(new MenuItem({ label: 'MenuItem2', type: 'checkbox', checked: true }))

window.addEventListener('contextmenu', (e) => {
  e.preventDefault()
  menu.popup({ window: remote.getCurrentWindow() })
}, false)
</script>
```

## Notas sobre el menú de la aplicación en macOS

macOS tiene un estilo de menú de aplicaciones completamente diferente de Windows y Linux. Aquí hay algunas notas sobre cómo hacer que el menú de su aplicación sea más parecido a un nativo.

### Menús Estándar

On macOS there are many system-defined standard menus, like the [`Services`](https://developer.apple.com/documentation/appkit/nsapplication/1428608-servicesmenu?language=objc) and `Windows` menus. Para hacer que el menú sea un menú estándar, se debe configurar el `role` del menú a uno de los siguientes y Electron los reconocerá y convertirá en menús estándares:

* `window`
* `help`
* `services`

### Acciones estándares de los elementos del menú

macOS ha proporcionado acciones estándares para algunos elementos del menú, como `About xxx`, `Hide xxx`, y `Hide Others`. Para establecer la acción de un elemento de menú en una acción estándar, debe establecer el atributo `función` del elemento del menú.

### Nombre del menú principal

En macOS, la etiqueta del primer elemento del menú de la aplicación siempre es su nombre de aplicación, sin importar la etiqueta que establezca. Para cambiarlo, modifique el archivo `Info.plist` file del conjunto de la app. Para mayor información, ver[About Information Property List Files](https://developer.apple.com/library/ios/documentation/general/Reference/InfoPlistKeyReference/Articles/AboutInformationPropertyListFiles.html).

## Menú de configuración para la ventana del navegador específico (*Linux* *Windows*)

El [`setMenu` method](https://github.com/electron/electron/blob/master/docs/api/browser-window.md#winsetmenumenu-linux-windows) de las ventanas del navegador pueden configurar el menú de ciertas ventanas del navegador.

## La posición del elemento del menú

You can make use of `before`, `after`, `beforeGroupContaining`, `afterGroupContaining` and `id` to control how the item will be placed when building a menu with `Menu.buildFromTemplate`.

* `before` - Inserts this item before the item with the specified label. If the referenced item doesn't exist the item will be inserted at the end of the menu. Also implies that the menu item in question should be placed in the same “group” as the item.
* `after` - Inserts this item after the item with the specified label. If the referenced item doesn't exist the item will be inserted at the end of the menu. Also implies that the menu item in question should be placed in the same “group” as the item.
* `beforeGroupContaining` - Provides a means for a single context menu to declare the placement of their containing group before the containing group of the item with the specified label.
* `afterGroupContaining` - Provides a means for a single context menu to declare the placement of their containing group after the containing group of the item with the specified label.

Por defecto, los elementos se insertarán en el orden en que existen en la plantilla, a menos que se utilice una de las palabras clave de posicionamiento especificadas.

### Ejemplos

Plantilla:

```javascript
[
  { id: '1', label: 'one' },
  { id: '2', label: 'two' },
  { id: '3', label: 'three' },
  { id: '4', label: 'four' }
]
```

Menú:

```sh
<br />- 1
- 2
- 3
- 4
```

Plantilla:

```javascript
[
  { id: '1', label: 'one' },
  { type: 'separator' },
  { id: '3', label: 'three', beforeGroupContaining: ['1'] },
  { id: '4', label: 'four', afterGroupContaining: ['2'] },
  { type: 'separator' },
  { id: '2', label: 'two' }
]
```

Menú:

```sh
<br />- 3
- 4
- ---
- 1
- ---
- 2
```

Plantilla:

```javascript
[
  { id: '1', label: 'one', after: ['3'] },
  { id: '2', label: 'two', before: ['1'] },
  { id: '3', label: 'three' }
]
```

Menú:

```sh
<br />- ---
- 3
- 2
- 1
```