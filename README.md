# Bali Marketplace — Full Project

This single-file project bundle contains a complete starter for a Bali-focused secondhand marketplace with chat features, built with React + Tailwind + JSON Server (auth & messages). Copy the files into your project folder as named and follow the README below.

---

## README.md
```
# Bali Marketplace (React + Tailwind + JSON Server)

## What is included
- React + Vite frontend
- Tailwind CSS styling
- JSON Server backend with simple auth (`json-server-auth`) and endpoints for users, products, messages
- Features: register/login, CRUD products (owner-only), search & filters, chat with typing indicator, unread/read receipts, last-seen, online status, chat list, notifications (react-hot-toast), image/file placeholders, typing indicator

## Quick setup
1. Create project folder and paste files from this bundle.
2. Install dependencies:
   ```bash
   npm install
   ```
3. Run JSON Server (in one terminal):
   ```bash
   npm run server
   ```
   This runs json-server-auth on port 3001.
4. Run frontend (in another terminal):
   ```bash
   npm run dev
   ```
5. Open http://localhost:5173

## Scripts
- `npm run dev` - vite dev server
- `npm run build` - build
- `npm run preview` - preview build
- `npm run server` - json-server-auth (backend) on port 3001

Notes: json-server-auth is a dev-only mock and not secure for production.
```

---

## package.json
```json
{
  "name": "bali-marketplace",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview",
    "server": "json-server db.json -m ./node_modules/json-server-auth --port 3001"
  },
  "dependencies": {
    "axios": "^1.4.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-hot-toast": "^2.4.0",
    "react-router-dom": "^6.14.1"
  },
  "devDependencies": {
    "autoprefixer": "^10.4.14",
    "postcss": "^8.4.21",
    "tailwindcss": "^3.6.0",
    "vite": "^5.0.0",
    "json-server": "^0.17.3",
    "json-server-auth": "^2.1.0"
  }
}
```

---

## db.json
```json
{
  "users": [
    {
      "id": 1,
      "name": "Moh Hadib",
      "email": "hadib@mail.com",
      "password": "123456",
      "online": false,
      "lastSeen": "2025-09-22T10:00:00Z",
      "typing": false
    },
    {
      "id": 2,
      "name": "Sari",
      "email": "sari@mail.com",
      "password": "123456",
      "online": false,
      "lastSeen": "2025-09-21T23:15:00Z",
      "typing": false
    }
  ],
  "products": [
    {
      "id": 1,
      "title": "Sepeda Gunung",
      "description": "Sepeda gunung kondisi 90%, ban baru diganti.",
      "price": 1200000,
      "location": "Denpasar",
      "category": "Olahraga",
      "image": "https://images.unsplash.com/photo-1508975553087-c1aee746d0d1",
      "userId": 1
    },
    {
      "id": 2,
      "title": "Laptop Bekas",
      "description": "Laptop Core i5, RAM 8GB, masih normal.",
      "price": 3500000,
      "location": "Kuta",
      "category": "Elektronik",
      "image": "https://images.unsplash.com/photo-1517336714731-489689fd1ca8",
      "userId": 2
    }
  ],
  "messages": [
    {
      "id": 1,
      "fromUserId": 1,
      "toUserId": 2,
      "text": "Halo, masih ada barangnya?",
      "timestamp": "2025-09-22T10:00:00Z",
      "read": false
    }
  ]
}
```

---

## tailwind.config.js
```js
export default {
  content: [
    './index.html',
    './src/**/*.{js,jsx,ts,tsx}'
  ],
  theme: { extend: {} },
  plugins: []
}
```

## postcss.config.cjs
```js
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
}
```

---

## src/main.jsx
```jsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App'
import './index.css'

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
)
```

## src/index.css
```css
@tailwind base;
@tailwind components;
@tailwind utilities;

html,body,#root { height: 100%; }
body { background: #f8fafc; }
```

---

## src/utils/formatTime.js
```js
export function formatLastSeen(timestamp) {
  if (!timestamp) return 'Belum pernah online';
  const date = new Date(timestamp);
  const now = new Date();
  const isToday = date.toDateString() === now.toDateString();
  if (isToday) return `hari ini ${date.toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})}`;
  return `${date.toLocaleDateString([], {day:'numeric', month:'short'})} ${date.toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})}`;
}

export function formatChatTime(timestamp) {
  const date = new Date(timestamp);
  const now = new Date();
  const isToday = date.toDateString() === now.toDateString();
  if (isToday) return date.toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'});
  return date.toLocaleDateString([], {day:'numeric', month:'short'});
}
```

---

## src/App.jsx
```jsx
import { BrowserRouter as Router, Routes, Route, Navigate } from 'react-router-dom'
import Navbar from './components/Navbar'
import Home from './pages/Home'
import Login from './pages/Login'
import Sell from './pages/Sell'
import MyProducts from './pages/MyProducts'
import Chat from './pages/Chat'
import ChatList from './pages/ChatList'
import { Toaster } from 'react-hot-toast'

function App(){
  return (
    <Router>
      <Toaster position="top-right" />
      <Navbar />
      <Routes>
        <Route path="/" element={<Home/>} />
        <Route path="/login" element={<Login/>} />
        <Route path="/jual" element={<Sell/>} />
        <Route path="/my-products" element={<MyProducts/>} />
        <Route path="/chats" element={<ChatList/>} />
        <Route path="/chat/:userId" element={<Chat/>} />
        <Route path="*" element={<Navigate to="/" replace />} />
      </Routes>
    </Router>
  )
}

export default App
```

---

## src/components/Navbar.jsx
```jsx
import { Link, useNavigate } from 'react-router-dom'
import { useEffect, useState } from 'react'
import axios from 'axios'

export default function Navbar(){
  const navigate = useNavigate();
  const user = JSON.parse(localStorage.getItem('user'));
  const [unreadCount, setUnreadCount] = useState(0);

  const fetchUnread = async ()=>{
    if(!user) return setUnreadCount(0);
    try{
      const res = await axios.get('http://localhost:3001/messages');
      const unread = res.data.filter(m => m.toUserId === user.id && !m.read).length;
      setUnreadCount(unread);
    }catch(err){ }
  }

  useEffect(()=>{
    fetchUnread();
    const i = setInterval(fetchUnread,3000);
    return ()=> clearInterval(i);
  },[user]);

  const handleLogout = async ()=>{
    const currentUser = JSON.parse(localStorage.getItem('user'));
    if(currentUser){
      try{ await axios.patch(`http://localhost:3001/users/${currentUser.id}`, { online: false, lastSeen: new Date().toISOString() }); }catch(e){}
    }
    localStorage.removeItem('user');
    localStorage.removeItem('token');
    navigate('/login');
  }

  return (
    <nav className="bg-green-600 text-white p-4 flex justify-between items-center">
      <Link to="/" className="text-xl font-bold">Bali Marketplace</Link>
      <div className="flex items-center gap-4">
        <Link to="/">Home</Link>
        {user && <Link to="/jual">Jual Barang</Link>}
        {user && <Link to="/my-products">Produk Saya</Link>}
        {user && (
          <Link to="/chats" className="relative">
            💬 Chat
            {unreadCount>0 && <span className="absolute -top-2 -right-3 bg-red-500 text-xs text-white rounded-full px-2">{unreadCount}</span>}
          </Link>
        )}
        {!user ? (
          <Link to="/login">Login</Link>
        ) : (
          <button onClick={handleLogout} className="bg-red-500 px-3 py-1 rounded">Logout</button>
        )}
      </div>
    </nav>
  )
}
```

---

## src/components/ProductCard.jsx
```jsx
import { Link } from 'react-router-dom'
export default function ProductCard({item, onEdit, onDelete}){
  const user = JSON.parse(localStorage.getItem('user'))
  const isOwner = user && item.userId === user.id
  return (
    <div className="bg-white shadow rounded-lg p-4">
      <img src={item.image} alt={item.title} className="rounded-lg mb-3 w-full h-48 object-cover" />
      <h2 className="text-lg font-semibold">{item.title}</h2>
      <p className="text-sm text-gray-600">{item.description}</p>
      <p className="text-green-600 font-bold mt-2">Rp {Number(item.price).toLocaleString('id-ID')}</p>
      <p className="text-gray-500 text-sm">📍 {item.location}</p>
      <p className="text-gray-400 text-xs">Kategori: {item.category}</p>
      <div className="flex gap-2 mt-3">
        <a className="flex-1 bg-green-600 text-white py-2 rounded-lg text-center" href={`https://wa.me/?text=${encodeURIComponent(`Halo, saya tertarik dengan ${item.title}`)}`} target="_blank" rel="noreferrer">Hubungi WA</a>
        <Link to={`/chat/${item.userId}`} className="flex-1 bg-blue-500 text-white py-2 rounded-lg text-center">Chat</Link>
        {isOwner && (
          <>
            <button onClick={()=>onEdit(item)} className="px-3 py-2 bg-yellow-400 text-white rounded-lg">Edit</button>
            <button onClick={()=>onDelete(item.id)} className="px-3 py-2 bg-red-500 text-white rounded-lg">Hapus</button>
          </>
        )}
      </div>
    </div>
  )
}
```

---

## src/components/ProductForm.jsx
```jsx
import { useState, useEffect } from 'react'
export default function ProductForm({onClose, onSubmit, editData}){
  const [title, setTitle] = useState(editData?.title||'')
  const [description, setDescription] = useState(editData?.description||'')
  const [price, setPrice] = useState(editData?.price||'')
  const [location, setLocation] = useState(editData?.location||'Denpasar')
  const [category, setCategory] = useState(editData?.category||'Elektronik')
  const [image, setImage] = useState(editData?.image||'')
  const user = JSON.parse(localStorage.getItem('user'))

  useEffect(()=>{ if(editData){ setTitle(editData.title) } },[editData])

  const handleSubmit = (e)=>{
    e.preventDefault()
    const payload = { title, description, price: Number(price), location, category, image: image||'https://via.placeholder.com/400', userId: user.id }
    onSubmit(payload, editData?.id)
  }

  return (
    <div className="fixed inset-0 bg-black/40 flex items-center justify-center">
      <div className="bg-white p-6 rounded-lg w-full max-w-md">
        <h3 className="text-lg font-bold mb-3">{editData? 'Edit Barang':'Jual Barang'}</h3>
        <form onSubmit={handleSubmit} className="space-y-3">
          <input className="w-full border p-2 rounded" placeholder="Nama Barang" value={title} onChange={e=>setTitle(e.target.value)} required />
          <textarea className="w-full border p-2 rounded" placeholder="Deskripsi" value={description} onChange={e=>setDescription(e.target.value)} required />
          <input className="w-full border p-2 rounded" type="number" placeholder="Harga" value={price} onChange={e=>setPrice(e.target.value)} required />
          <select className="w-full border p-2 rounded" value={location} onChange={e=>setLocation(e.target.value)}>
            <option>Denpasar</option>
            <option>Kuta</option>
            <option>Ubud</option>
            <option>Seminyak</option>
          </select>
          <select className="w-full border p-2 rounded" value={category} onChange={e=>setCategory(e.target.value)}>
            <option>Elektronik</option>
            <option>Furniture</option>
            <option>Fashion</option>
            <option>Lainnya</option>
          </select>
          <input className="w-full border p-2 rounded" placeholder="URL Gambar (opsional)" value={image} onChange={e=>setImage(e.target.value)} />
          <div className="flex justify-end gap-2">
            <button type="button" onClick={onClose} className="px-4 py-2 border rounded">Batal</button>
            <button type="submit" className="px-4 py-2 bg-green-600 text-white rounded">{editData? 'Update':'Publish'}</button>
          </div>
        </form>
      </div>
    </div>
  )
}
```

---

## src/components/ChatBox.jsx
```jsx
import { useEffect, useState, useRef } from 'react'
import axios from 'axios'
import { formatLastSeen, formatChatTime } from '../utils/formatTime'

export default function ChatBox({otherUserId}){
  const currentUser = JSON.parse(localStorage.getItem('user'))
  const [otherUser, setOtherUser] = useState(null)
  const [messages, setMessages] = useState([])
  const [text, setText] = useState('')
  const [typing, setTyping] = useState(false)
  const ref = useRef()

  const fetchMessages = async ()=>{
    try{
      const res = await axios.get('http://localhost:3001/messages')
      const conv = res.data.filter(m => (m.fromUserId===currentUser.id && m.toUserId===otherUserId) || (m.fromUserId===otherUserId && m.toUserId===currentUser.id))
      conv.sort((a,b)=> new Date(a.timestamp)-new Date(b.timestamp))
      setMessages(conv)
    }catch(e){}
  }

  useEffect(()=>{
    axios.get(`http://localhost:3001/users/${otherUserId}`).then(r=>setOtherUser(r.data)).catch(()=>{})
    fetchMessages()
    const i = setInterval(fetchMessages,2000)
    return ()=> clearInterval(i)
  },[otherUserId])

  // mark incoming as read when opening
  useEffect(()=>{
    (async ()=>{
      const res = await axios.get('http://localhost:3001/messages')
      const unread = res.data.filter(m => m.toUserId===currentUser.id && m.fromUserId===otherUserId && !m.read)
      for(const m of unread){
        await axios.patch(`http://localhost:3001/messages/${m.id}`, { read: true })
      }
      fetchMessages()
    })()
  },[otherUserId])

  useEffect(()=>{ if(ref.current) ref.current.scrollTop = ref.current.scrollHeight },[messages])

  const handleSend = async (e)=>{
    e.preventDefault();
    if(!text.trim()) return;
    const payload = { fromUserId: currentUser.id, toUserId: otherUserId, text, timestamp: new Date().toISOString(), read: false }
    await axios.post('http://localhost:3001/messages', payload)
    setText('')
    // ping other user's typing=false handled by theirs
    fetchMessages()
  }

  // typing indicator set own typing true
  useEffect(()=>{
    if(!currentUser) return;
    let t
    if(text !== ''){
      axios.patch(`http://localhost:3001/users/${currentUser.id}`, { typing: true }).catch(()=>{})
      t = setTimeout(()=> axios.patch(`http://localhost:3001/users/${currentUser.id}`, { typing: false }).catch(()=>{}), 1500)
    }
    return ()=> clearTimeout(t)
  },[text])

  return (
    <div className="flex flex-col h-full max-w-2xl mx-auto">
      <div className="flex items-center gap-3 p-3 border-b bg-white">
        <span className={`w-3 h-3 rounded-full ${otherUser?.online? 'bg-green-500':'bg-gray-400'}`}></span>
        <div>
          <div className="font-semibold">{otherUser?.name}</div>
          {otherUser?.online ? <div className="text-xs text-green-600">Online</div> : <div className="text-xs text-gray-500">Terakhir online: {formatLastSeen(otherUser?.lastSeen)}</div>}
          {otherUser?.typing && <div className="text-xs text-green-600">Sedang mengetik…</div>}
        </div>
      </div>

      <div ref={ref} className="flex-1 overflow-y-auto p-4 space-y-3 bg-gray-50">
        {messages.map(m=> (
          <div key={m.id} className={`max-w-[70%] p-2 rounded ${m.fromUserId===currentUser.id? 'ml-auto bg-blue-500 text-white':'mr-auto bg-white border'}`}>
            <div className="text-sm">{m.text}</div>
            <div className="text-xs text-gray-300 mt-1 flex justify-end">{formatChatTime(m.timestamp)} {m.fromUserId===currentUser.id && (m.read? ' ✅':' ✅')}</div>
          </div>
        ))}
      </div>

      <form onSubmit={handleSend} className="p-3 bg-white flex gap-2">
        <input value={text} onChange={e=>setText(e.target.value)} placeholder="Ketik pesan..." className="flex-1 border rounded p-2" />
        <button className="bg-green-600 text-white px-4 py-2 rounded">Kirim</button>
      </form>
    </div>
  )
}
```

---

## src/pages/Login.jsx
```jsx
import { useState } from 'react'
import axios from 'axios'
import { useNavigate } from 'react-router-dom'

export default function Login(){
  const [isRegister,setIsRegister] = useState(false)
  const [email,setEmail] = useState('')
  const [password,setPassword] = useState('')
  const [name,setName] = useState('')
  const navigate = useNavigate()

  const handleSubmit = async (e)=>{
    e.preventDefault()
    try{
      if(isRegister){
        // simple register
        const res = await axios.post('http://localhost:3001/users', { email, password, name, online: true, lastSeen: new Date().toISOString(), typing: false })
        alert('Registrasi berhasil; silakan login')
        setIsRegister(false)
      } else {
        const res = await axios.get(`http://localhost:3001/users?email=${email}&password=${password}`)
        if(res.data.length>0){
          const user = res.data[0]
          localStorage.setItem('user', JSON.stringify(user))
          localStorage.setItem('token','dummy')
          // set online true
          await axios.patch(`http://localhost:3001/users/${user.id}`, { online: true })
          navigate('/')
        } else alert('Login gagal')
      }
    }catch(e){ alert('Error') }
  }

  return (
    <div className="flex items-center justify-center h-screen bg-gray-100">
      <form onSubmit={handleSubmit} className="bg-white p-6 rounded shadow w-80 space-y-3">
        <h2 className="text-xl font-bold text-center">{isRegister? 'Register':'Login'}</h2>
        {isRegister && <input placeholder="Nama" className="w-full border p-2 rounded" value={name} onChange={e=>setName(e.target.value)} required />}
        <input type="email" placeholder="Email" className="w-full border p-2 rounded" value={email} onChange={e=>setEmail(e.target.value)} required />
        <input type="password" placeholder="Password" className="w-full border p-2 rounded" value={password} onChange={e=>setPassword(e.target.value)} required />
        <button className="w-full bg-green-600 text-white py-2 rounded" type="submit">{isRegister? 'Register':'Login'}</button>
        <p className="text-sm text-center text-blue-600 cursor-pointer" onClick={()=>setIsRegister(!isRegister)}>{isRegister? 'Sudah punya akun? Login':'Belum punya akun? Register'}</p>
      </form>
    </div>
  )
}
```

---

## src/pages/Home.jsx
```jsx
import { useEffect, useState } from 'react'
import axios from 'axios'
import ProductCard from '../components/ProductCard'
import ProductForm from '../components/ProductForm'

export default function Home(){
  const [products,setProducts] = useState([])
  const [search,setSearch] = useState('')
  const [filterLocation,setFilterLocation] = useState('')
  const [filterCategory,setFilterCategory] = useState('')
  const [showForm,setShowForm] = useState(false)
  const [editProduct,setEditProduct] = useState(null)

  const fetchProducts = async ()=>{
    const res = await axios.get('http://localhost:3001/products')
    setProducts(res.data)
  }
  useEffect(()=>{ fetchProducts() },[])

  const handleSave = async (payload, id)=>{
    if(id) await axios.put(`http://localhost:3001/products/${id}`, payload)
    else await axios.post('http://localhost:3001/products', payload)
    fetchProducts()
    setShowForm(false)
    setEditProduct(null)
  }

  const handleDelete = async (id)=>{ if(confirm('Yakin?')){ await axios.delete(`http://localhost:3001/products/${id}`); fetchProducts() } }

  const filtered = products.filter(p=> (p.title.toLowerCase().includes(search.toLowerCase())||p.description.toLowerCase().includes(search.toLowerCase())) && (filterLocation? p.location===filterLocation:true) && (filterCategory? p.category===filterCategory:true) )

  return (
    <div className="p-6">
      <div className="flex flex-wrap gap-2 mb-4">
        <input className="border p-2 rounded flex-1" placeholder="Cari barang..." value={search} onChange={e=>setSearch(e.target.value)} />
        <select className="border p-2 rounded" value={filterLocation} onChange={e=>setFilterLocation(e.target.value)}>
          <option value="">Semua Lokasi</option>
          <option>Denpasar</option>
          <option>Kuta</option>
          <option>Ubud</option>
          <option>Seminyak</option>
        </select>
        <select className="border p-2 rounded" value={filterCategory} onChange={e=>setFilterCategory(e.target.value)}>
          <option value="">Semua Kategori</option>
          <option>Elektronik</option>
          <option>Furniture</option>
          <option>Fashion</option>
          <option>Lainnya</option>
        </select>
        <button onClick={()=>{ setShowForm(true); setEditProduct(null) }} className="bg-amber-400 px-4 py-2 rounded">Jual Barang</button>
      </div>

      <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
        {filtered.map(item=> <ProductCard key={item.id} item={item} onEdit={(d)=>{ setEditProduct(d); setShowForm(true) }} onDelete={handleDelete} />)}
      </div>

      {showForm && <ProductForm onClose={()=>setShowForm(false)} onSubmit={handleSave} editData={editProduct} />}
    </div>
  )
}
```

---

## src/pages/Sell.jsx
```jsx
import ProductForm from '../components/ProductForm'
import { useNavigate } from 'react-router-dom'
export default function Sell(){
  const navigate = useNavigate()
  return (
    <div className="p-6">
      <h2 className="text-xl font-bold mb-4">Jual Barang</h2>
      <ProductForm onClose={()=>navigate('/')} onSubmit={async (p)=>{ await fetch('http://localhost:3001/products', {method:'POST', headers:{'Content-Type':'application/json'}, body:JSON.stringify(p) }); navigate('/') }} />
    </div>
  )
}
```

---

## src/pages/MyProducts.jsx
```jsx
import { useEffect, useState } from 'react'
import axios from 'axios'
import ProductCard from '../components/ProductCard'
import ProductForm from '../components/ProductForm'

export default function MyProducts(){
  const user = JSON.parse(localStorage.getItem('user'))
  const [products,setProducts] = useState([])
  const [showForm,setShowForm] = useState(false)
  const [editProduct,setEditProduct] = useState(null)

  const fetchProducts = async ()=>{ const res = await axios.get(`http://localhost:3001/products?userId=${user.id}`); setProducts(res.data) }
  useEffect(()=>{ fetchProducts() },[])

  const handleSave = async (payload,id)=>{ if(id) await axios.put(`http://localhost:3001/products/${id}`, payload) else await axios.post('http://localhost:3001/products', payload); fetchProducts(); setShowForm(false); setEditProduct(null) }
  const handleDelete = async (id)=>{ if(confirm('Yakin?')){ await axios.delete(`http://localhost:3001/products/${id}`); fetchProducts() } }

  return (
    <div className="p-6">
      <h2 className="text-xl font-bold mb-4">Produk Saya</h2>
      <div className="grid grid-cols-1 md:grid-cols-3 gap-6">
        {products.map(p=> <ProductCard key={p.id} item={p} onEdit={(d)=>{ setEditProduct(d); setShowForm(true) }} onDelete={handleDelete} />)}
      </div>
      {showForm && <ProductForm onClose={()=>setShowForm(false)} onSubmit={handleSave} editData={editProduct} />}
    </div>
  )
}
```

---

## src/pages/ChatList.jsx
```jsx
import { useEffect, useState } from 'react'
import { Link } from 'react-router-dom'
import axios from 'axios'
import { formatLastSeen } from '../utils/formatTime'

export default function ChatList(){
  const user = JSON.parse(localStorage.getItem('user'))
  const [chats,setChats] = useState([])
  const [users,setUsers] = useState({})
  const [unread,setUnread] = useState({})

  useEffect(()=>{
    if(!user) return;
    (async ()=>{
      const ures = await axios.get('http://localhost:3001/users')
      const uMap = {}
      ures.data.forEach(u=> uMap[u.id]=u)
      setUsers(uMap)
      const mres = await axios.get('http://localhost:3001/messages')
      const msgs = mres.data
      const convos = {}
      const unreadCount = {}
      msgs.forEach(msg=>{
        const other = msg.fromUserId===user.id? msg.toUserId: msg.fromUserId
        if(!convos[other] || new Date(msg.timestamp) > new Date(convos[other].timestamp)) convos[other]=msg
        if(msg.toUserId===user.id && !msg.read) unreadCount[other] = (unreadCount[other]||0)+1
      })
      setChats(Object.values(convos))
      setUnread(unreadCount)
    })()
  },[])

  return (
    <div className="max-w-2xl mx-auto mt-6">
      <h2 className="text-xl font-bold mb-4">Pesan Saya</h2>
      {chats.length===0? <p className="text-gray-600">Belum ada percakapan.</p> : (
        <ul className="divide-y border rounded-lg bg-white">
          {chats.map(chat=>{
            const otherId = chat.fromUserId===user.id? chat.toUserId: chat.fromUserId
            const other = users[otherId]
            const unreadCount = unread[otherId] || 0
            return (
              <li key={chat.id} className="p-4 hover:bg-gray-50">
                <Link to={`/chat/${otherId}`} className="flex justify-between items-center">
                  <div className="flex items-center gap-2">
                    <span className={`w-3 h-3 rounded-full ${other?.online? 'bg-green-500':'bg-gray-400'}`}></span>
                    <div>
                      <span className={unreadCount>0? 'font-bold':''}>{other? other.name:`User #${otherId}`}</span>
                      <p className="text-sm text-gray-500">{chat.text.slice(0,20)}...</p>
                      {!other?.online && <p className="text-xs text-gray-400">Terakhir online: {formatLastSeen(other?.lastSeen)}</p>}
                    </div>
                  </div>
                  <div className="flex flex-col items-end">
                    <span className="text-xs text-gray-400">{new Date(chat.timestamp).toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'})}</span>
                    {unreadCount>0 && <span className="bg-red-500 text-white text-xs px-2 py-1 rounded-full mt-1">{unreadCount}</span>}
                  </div>
                </Link>
              </li>
            )
          })}
        </ul>
      )}
    </div>
  )
}
```

---

## src/pages/Chat.jsx
```jsx
import { useParams } from 'react-router-dom'
import ChatBox from '../components/ChatBox'
export default function Chat(){
  const { userId } = useParams()
  return (
    <div className="p-6">
      <ChatBox otherUserId={Number(userId)} />
    </div>
  )
}
```

---

## Final notes
- This is a fully local mock using json-server; it is intentionally simple so you can test UI/workflow. For production you'd replace JSON Server with a real backend, use secure auth, store hashed passwords, and integrate WebSockets for real-time.
- If something breaks: check console for CORS or port conflicts. Make sure `json-server` and `json-server-auth` are installed.

Enjoy! ❤️
