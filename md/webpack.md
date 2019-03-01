webpack可以直接使用import和export而不需要借助第三方工具,如：
```javascript
import MyModule from './my-module.js';
import { NamedExport } from './other-module.js';
```
但是并不支持除import和export之外的ES6代码，这需要浏览器的支持。如果需要使用ES6新特性，请借助Babel或webpack的loader转换。