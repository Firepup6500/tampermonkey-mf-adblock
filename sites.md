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
    interval: 0,
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
    interval: 0,
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
    timeout: 2000,
    interval: 0,
    trueRemove: false
}
```

  </td>
</tr>

  </tbody>
</table>
