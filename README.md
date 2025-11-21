# ecommerce-frontend-page-
e commerce frontend page 
import React, { useState } from 'react';

export default function ECommerceFrontend() {
  const [cart, setCart] = useState([]);
  const [search, setSearch] = useState('');
  const [selectedCategory, setSelectedCategory] = useState('All');
  const [sortOrder, setSortOrder] = useState('default');
  const [showPaymentPopup, setShowPaymentPopup] = useState(false);
  const [paymentMethod, setPaymentMethod] = useState('');
  const [showReceipt, setShowReceipt] = useState(false);

  const products = [
    { id: 1, name: 'Nike Air Zoom Pegasus 40', price: 11999, category: 'Shoes', image: 'https://images.unsplash.com/photo-1606813908750-82e548dff3d7' },
    { id: 2, name: 'Apple iPhone 15 Pro', price: 134900, category: 'Electronics', image: 'https://images.unsplash.com/photo-1695048133070-43c7d06a1e16' },
    { id: 3, name: 'Levi’s 511 Slim Fit Jeans', price: 4999, category: 'Clothing', image: 'https://images.unsplash.com/photo-1618354691438-25e22b4e77d2' },
    { id: 4, name: 'Samsung Galaxy S24 Ultra', price: 139999, category: 'Electronics', image: 'https://images.unsplash.com/photo-1701694638764-f27c384aa5da' },
    { id: 5, name: 'Adidas Ultraboost 22', price: 15999, category: 'Shoes', image: 'https://images.unsplash.com/photo-1606814723633-d52f98a3b8c2' },
    { id: 6, name: 'Casio G-Shock Watch', price: 8999, category: 'Accessories', image: 'https://images.unsplash.com/photo-1612810806361-5c09be266e9d' },
  ];

  const filteredProducts = products
    .filter(p => p.name.toLowerCase().includes(search.toLowerCase()) && (selectedCategory === 'All' || p.category === selectedCategory))
    .sort((a, b) => {
      if (sortOrder === 'asc') return a.price - b.price;
      if (sortOrder === 'desc') return b.price - a.price;
      return 0;
    });

  const addToCart = (product) => {
    setCart(prev => {
      const exists = prev.find(p => p.id === product.id);
      if (exists) return prev.map(p => p.id === product.id ? { ...p, qty: p.qty + 1 } : p);
      return [...prev, { ...product, qty: 1 }];
    });
  };

  const removeFromCart = (id) => setCart(cart.filter(p => p.id !== id));

  const total = cart.reduce((sum, p) => sum + p.price * p.qty, 0);

  const handleCheckout = () => {
    if (cart.length === 0) return;
    setShowPaymentPopup(true);
  };

  const handlePaymentSelect = (method) => {
    setPaymentMethod(method);
  };

  const handlePaymentSubmit = () => {
    if (paymentMethod === 'NetBanking' || paymentMethod === 'UPI') {
      setShowPaymentPopup(false);
      setShowReceipt(true);
    }
  };

  const closeReceipt = () => {
    setShowReceipt(false);
    setCart([]);
    setPaymentMethod('');
  };

  return (
    <div className="min-h-screen bg-gray-100 p-4">
      <header className="bg-blue-600 text-white p-4 rounded-lg mb-4 flex justify-between items-center">
        <h1 className="text-2xl font-bold">ShopEase</h1>
        <div className="flex gap-3 items-center">
          <input type="text" placeholder="Search products..." value={search} onChange={(e) => setSearch(e.target.value)} className="px-3 py-1 rounded text-black" />
          <select onChange={(e) => setSelectedCategory(e.target.value)} className="text-black px-2 py-1 rounded">
            <option>All</option>
            <option>Electronics</option>
            <option>Clothing</option>
            <option>Shoes</option>
            <option>Accessories</option>
          </select>
          <select onChange={(e) => setSortOrder(e.target.value)} className="text-black px-2 py-1 rounded">
            <option value="default">Sort</option>
            <option value="asc">Price: Low to High</option>
            <option value="desc">Price: High to Low</option>
          </select>
          <button className="bg-white text-blue-600 font-bold px-3 py-1 rounded">Cart ({cart.length})</button>
        </div>
      </header>

      <main className="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4">
        {filteredProducts.map(p => (
          <div key={p.id} className="bg-white rounded-lg shadow p-3 hover:shadow-lg transition">
            <img src={`${p.image}?auto=format&fit=crop&w=400&q=60`} alt={p.name} className="rounded-lg w-full h-48 object-cover" />
            <h2 className="mt-2 font-semibold text-lg">{p.name}</h2>
            <p className="text-gray-600">₹{p.price.toLocaleString()}</p>
            <p className="text-sm text-gray-500">Category: {p.category}</p>
            <button onClick={() => addToCart(p)} className="mt-2 w-full bg-blue-600 text-white py-2 rounded-lg">Add to Cart</button>
          </div>
        ))}
      </main>

      {cart.length > 0 && (
        <aside className="fixed bottom-4 right-4 bg-white rounded-lg shadow-lg p-4 w-80">
          <h2 className="text-lg font-semibold mb-2">Your Cart</h2>
          {cart.map(item => (
            <div key={item.id} className="flex justify-between items-center border-b py-2">
              <span>{item.name}</span>
              <div>
                <span>₹{(item.price * item.qty).toLocaleString()}</span>
                <button onClick={() => removeFromCart(item.id)} className="text-red-500 ml-2">✕</button>
              </div>
            </div>
          ))}
          <div className="mt-3 font-bold">Total: ₹{total.toLocaleString()}</div>
          <button onClick={handleCheckout} className="mt-2 bg-green-600 w-full text-white py-2 rounded-lg">Checkout</button>
        </aside>
      )}

      {showPaymentPopup && (
        <div className="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
          <div className="bg-white p-6 rounded-lg w-96 text-center">
            <h2 className="text-xl font-bold mb-4">Select Payment Method</h2>
            <div className="flex flex-col gap-2 mb-4">
              <button onClick={() => handlePaymentSelect('NetBanking')} className={`py-2 rounded-lg border ${paymentMethod === 'NetBanking' ? 'bg-blue-600 text-white' : 'bg-gray-100'}`}>NetBanking</button>
              <button onClick={() => handlePaymentSelect('UPI')} className={`py-2 rounded-lg border ${paymentMethod === 'UPI' ? 'bg-blue-600 text-white' : 'bg-gray-100'}`}>UPI Payment</button>
            </div>

            {paymentMethod === 'NetBanking' && (
              <div className="text-left">
                <label className="block mb-1">Card Number:</label>
                <input type="text" placeholder="XXXX-XXXX-XXXX-XXXX" className="border w-full mb-2 px-2 py-1 rounded" />
                <label className="block mb-1">Expiry Date:</label>
                <input type="month" className="border w-full mb-2 px-2 py-1 rounded" />
                <label className="block mb-1">CVV:</label>
                <input type="password" placeholder="***" className="border w-full mb-3 px-2 py-1 rounded" />
              </div>
            )}

            {paymentMethod === 'UPI' && (
              <div className="flex flex-col items-center">
                <p className="mb-2">Scan this QR Code to pay:</p>
                <img src="https://api.qrserver.com/v1/create-qr-code/?data=upi://pay?pa=shop@upi&amount=1&currency=INR&size=150x150" alt="QR Code" className="w-40 h-40 mb-2" />
                <p className="text-sm text-gray-600">Once payment is done, click Confirm Payment</p>
              </div>
            )}

            <button onClick={handlePaymentSubmit} className="bg-green-600 text-white py-2 px-4 rounded-lg mt-4 w-full">Confirm Payment</button>
          </div>
        </div>
      )}

      {showReceipt && (
        <div className="fixed inset-0 bg-black bg-opacity-60 flex items-center justify-center z-50">
          <div className="bg-white p-6 rounded-lg shadow-lg w-96 text-center animate-fadeIn">
            <h2 className="text-xl font-bold mb-4 text-green-600">✅ Payment Successful!</h2>
            <p className="text-gray-700 mb-3">Thank you for shopping with ShopEase!</p>
            <div className="border-t pt-3 text-left">
              <h3 className="font-semibold mb-2">Receipt</h3>
              {cart.map(item => (
                <div key={item.id} className="flex justify-between text-sm mb-1">
                  <span>{item.name} (x{item.qty})</span>
                  <span>₹{(item.price * item.qty).toLocaleString()}</span>
                </div>
              ))}
              <hr className="my-2" />
              <p className="font-bold text-right">Total: ₹{total.toLocaleString()}</p>
            </div>
            <button onClick={closeReceipt} className="mt-4 bg-blue-600 text-white px-4 py-2 rounded-lg w-full">Close</button>
          </div>
        </div>
      )}
    </div>
  );
}

