<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Sakura Raw</title>
<style>
body{background:#0d0d0d;color:#e0e0e0;font-family:Courier New,monospace;padding:20px;margin:0}
h1{color:#f8b4c8;text-align:center}
.box{max-width:800px;margin:0 auto}
textarea{width:100%;min-height:200px;background:#111;border:1px solid #333;border-radius:4px;color:#e0e0e0;font-family:Courier New,monospace;font-size:14px;padding:10px;outline:none;display:block;margin:10px 0}
input{width:100%;padding:10px;background:#111;border:1px solid #333;border-radius:4px;color:#e0e0e0;font-family:Courier New,monospace;font-size:14px;outline:none;display:block;margin:10px 0}
button{padding:10px 20px;border:none;border-radius:4px;font-family:Courier New,monospace;font-size:14px;font-weight:bold;cursor:pointer;margin:5px 5px 5px 0}
.btn{padding:10px 20px;border:none;border-radius:4px;font-family:Courier New,monospace;font-size:14px;font-weight:bold;cursor:pointer;margin:5px 5px 5px 0}
.b1{background:#f8b4c8;color:#000}
.b2{background:#333;color:#e0e0e0}
.item{background:#1a1a1a;border:1px solid #333;border-radius:4px;padding:10px 15px;margin:8px 0;display:flex;justify-content:space-between;align-items:center}
.name{color:#f8b4c8;font-weight:bold}
.rlink{color:#f8b4c8;font-size:13px;text-decoration:none;border:1px solid #f8b4c8;padding:4px 10px;border-radius:3px;margin-right:5px}
.rlink:hover{background:#f8b4c8;color:#000}
.dbtn{color:#ff4d4d;border:1px solid #ff4d4d;background:transparent;padding:4px 10px;border-radius:3px;cursor:pointer;font-size:13px}
.dbtn:hover{background:#ff4d4d;color:#fff}
.footer{text-align:center;color:#444;padding:20px;font-size:12px}
#msg{position:fixed;bottom:20px;right:20px;background:#1a1a1a;border:1px solid #f8b4c8;padding:10px 20px;border-radius:4px;color:#f8b4c8;font-size:13px;opacity:0;transition:all 0.3s}
#msg.show{opacity:1}
</style>
</head>
<body>
<div class="box">
<h1>SAKURA RAW</h1>
<textarea id="txt" placeholder="Nhap script cua ban"></textarea>
<input type="text" id="nam" placeholder="Ten raw">
<div>
<button class="b1" onclick="luu()">Luu</button>
<button class="b2" onclick="xoaform()">Moi</button>
</div>
<div id="danhsach" style="margin-top:20px"></div>
<div class="footer">sakura raw</div>
</div>
<div id="msg"></div>
<script>
let K = 'sakura_data'
function lay() {
  let d = localStorage.getItem(K)
  if (d) { try { return JSON.parse(d) } catch(e) {} }
  return []
}
function cap(d) {
  localStorage.setItem(K, JSON.stringify(d))
}
function idmoi() {
  let c = 'abcdefghijklmnopqrstuvwxyz0123456789'
  let r = ''
  for (let i = 0; i < 8; i++) {
    r += c[Math.floor(Math.random() * c.length)]
  }
  return r
}
function thoigian() {
  return new Date().toISOString()
}
function toast(s) {
  let t = document.getElementById('msg')
  t.textContent = s
  t.classList.add('show')
  setTimeout(function() { t.classList.remove('show') }, 2500)
}
function xoaform() {
  document.getElementById('txt').value = ''
  document.getElementById('nam').value = ''
}
function luu() {
  let content = document.getElementById('txt').value
  let name = document.getElementById('nam').value.trim()
  if (!content) { toast('Nhap noi dung'); return }
  if (!name) { toast('Nhap ten raw'); return }
  let data = lay()
  let found = false
  for (let i = 0; i < data.length; i++) {
    if (data[i].name == name) {
      data[i].content = content
      data[i].updated = thoigian()
      found = true
      break
    }
  }
  if (!found) {
    data.push({id: idmoi(), name: name, content: content, created: thoigian(), updated: thoigian()})
  }
  cap(data)
  render()
  xoaform()
  toast('Da luu: ' + name)
}
function xoa(name) {
  if (!confirm('Xoa ' + name + '?')) return
  let data = lay()
  let moi = []
  for (let i = 0; i < data.length; i++) {
    if (data[i].name != name) {
      moi.push(data[i])
    }
  }
  cap(moi)
  render()
  toast('Da xoa: ' + name)
}
function raw(name) {
  let data = lay()
  let p = null
  for (let i = 0; i < data.length; i++) {
    if (data[i].name == name) { p = data[i]; break }
  }
  if (!p) { toast('Khong tim thay'); return }
  let w = window.open('', '_blank')
  let esc = p.content.replace(/&/g, '&amp;').replace(/</g, '&lt;').replace(/>/g, '&gt;')
  let html = '<!DOCTYPE html><html><head><meta charset="UTF-8"><title>' + p.name + ' raw</title><style>body{background:#0d0d0d;color:#e0e0e0;font-family:Courier New,monospace;font-size:14px;padding:20px;white-space:pre-wrap;word-break:break-all}pre{margin:0}</style></head><body><pre>' + esc + '</pre></body></html>'
  w.document.write(html)
  w.document.close()
}
function render() {
  let c = document.getElementById('danhsach')
  let data = lay()
  if (data.length == 0) {
    c.innerHTML = '<div style="color:#555;text-align:center;padding:20px">Chua co raw nao</div>'
    return
  }
  data.sort(function(a, b) { return b.updated.localeCompare(a.updated) })
  let html = ''
  for (let i = 0; i < data.length; i++) {
    let p = data[i]
    let u = window.location.origin + window.location.pathname + '?raw=' + encodeURIComponent(p.name)
    html += '<div class="item"><div class="name">' + p.name + '</div><div><a href="' + u + '" target="_blank" class="rlink">RAW</a><button class="dbtn" onclick="xoa(\'' + p.name.replace(/'/g, "\\'") + '\')">Xoa</button></div></div>'
  }
  c.innerHTML = html
}
function checkRaw() {
  let s = window.location.search
  if (s.indexOf('raw=') >= 0) {
    let params = new URLSearchParams(s)
    let rawName = params.get('raw')
    if (rawName) {
      let data = lay()
      let p = null
      for (let i = 0; i < data.length; i++) {
        if (data[i].name == rawName) { p = data[i]; break }
      }
      if (p) {
        let esc = p.content.replace(/&/g, '&amp;').replace(/</g, '&lt;').replace(/>/g, '&gt;')
        document.body.innerHTML = '<pre style="background:#0d0d0d;color:#e0e0e0;font-family:Courier New,monospace;font-size:14px;padding:20px;margin:0;white-space:pre-wrap;word-break:break-all">' + esc + '</pre>'
        document.title = p.name + ' raw'
      } else {
        document.body.innerHTML = '<pre style="background:#0d0d0d;color:#ff4d4d;font-family:monospace;font-size:14px;padding:20px">404 - ' + rawName + ' khong ton tai</pre>'
        document.title = '404 not found'
      }
    }
  }
}
if (window.location.search.indexOf('raw=') >= 0) {
  checkRaw()
} else {
  render()
}
</script>
</body>
</html>
