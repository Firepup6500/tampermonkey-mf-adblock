<table>
  <thead>
    <tr>
      <th>Site</th>
      <th>Code</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>nexusmods.com</td>
      <td>

```javascript
'.*\.?nexusmods\.com': {
    remove: ['.ads','#nn_skinl','#nn_skinr','.nn-sticky','.nn_player'],
    interaction: true,
    timeout: 2000,
    trueRemove: true
}
```
  </td>
</tr>
<tr>
  <td>dtf.ru</td>
  <td>

```javascript
'.*\.?dtf\.ru': {
    remove: ['.propaganda'],
    interaction: true,
    timeout: 3000,
    trueRemove: true
}
```
  </td>
</tr>
<tr>
  <td>parade.com</td>
  <td>
    
```javascript
'.*\.?parade\.com': {
    remove: ['.m-in-content-ad','.m-in-content-ad-row','.m-outbrain','.m-fixedbottom-ad--container','.m-ad'],
    interaction: true,
    timeout: 2000
}
```
  </td>
</tr>
<tr>
  <td>fandom.com</td>
  <td>

```javascript
'.*\.?fandom\.com': {
    remove: ['.top-ads-container','.bottom-ads-container','#WikiaBar','.notifications-placeholder','.gpt-ad'],
    interaction: true,
    timeout: 2000,
    trueRemove: true
},
```
  </td>
</tr>
  </tbody>
</table>
