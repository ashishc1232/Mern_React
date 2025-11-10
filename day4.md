import React, { useState } from 'react';
import { Search } from 'lucide-react';

export default function App() {
  const [searchTerm, setSearchTerm] = useState('');
  const [selectedCategory, setSelectedCategory] = useState('All');

  // Dynamic product array
  const products = [
    { id: 1, name: 'Laptop', price: 899, category: 'Electronics' },
    { id: 2, name: 'Headphones', price: 199, category: 'Electronics' },
    { id: 3, name: 'Coffee Mug', price: 15, category: 'Home' },
    { id: 4, name: 'Desk Chair', price: 299, category: 'Furniture' },
    { id: 5, name: 'Phone Case', price: 25, category: 'Accessories' },
    { id: 6, name: 'Water Bottle', price: 20, category: 'Home' },
    { id: 7, name: 'Keyboard', price: 79, category: 'Electronics' },
    { id: 8, name: 'Backpack', price: 59, category: 'Accessories' },
  ];

  // Get unique categories
  const categories = ['All', ...new Set(products.map(p => p.category))];

  // Filter products based on search and category
  const filteredProducts = products.filter(product => {
    const matchesSearch = product.name.toLowerCase().includes(searchTerm.toLowerCase());
    const matchesCategory = selectedCategory === 'All' || product.category === selectedCategory;
    return matchesSearch && matchesCategory;
  });

  return (
    <div className="min-h-screen bg-gradient-to-br from-purple-50 to-blue-50 p-8">
      <div className="max-w-4xl mx-auto">
        <h1 className="text-3xl font-bold text-gray-800 mb-8">Product List</h1>

        {/* Search Bar */}
        <div className="relative mb-6">
          <Search className="absolute left-3 top-3 text-gray-400" size={20} />
          <input
            type="text"
            placeholder="Search products..."
            value={searchTerm}
            onChange={(e) => setSearchTerm(e.target.value)}
            className="w-full pl-10 pr-4 py-3 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500"
          />
        </div>

        {/* Category Filter */}
        <div className="flex gap-2 mb-6 flex-wrap">
          {categories.map(category => (
            <button
              key={category}
              onClick={() => setSelectedCategory(category)}
              className={`px-4 py-2 rounded-lg transition-colors ${
                selectedCategory === category
                  ? 'bg-blue-600 text-white'
                  : 'bg-white text-gray-700 hover:bg-gray-100'
              }`}
            >
              {category}
            </button>
          ))}
        </div>

        {/* Products List - Dynamic Rendering */}
        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
          {filteredProducts.length === 0 ? (
            <p className="col-span-full text-center text-gray-500 py-8">
              No products found
            </p>
          ) : (
            filteredProducts.map(product => (
              <div
                key={product.id}
                className="bg-white rounded-lg shadow-md p-5 hover:shadow-lg transition-shadow"
              >
                <h3 className="text-lg font-semibold text-gray-800 mb-2">
                  {product.name}
                </h3>
                <p className="text-sm text-gray-500 mb-3">{product.category}</p>
                <p className="text-2xl font-bold text-blue-600">${product.price}</p>
              </div>
            ))
          )}
        </div>

        {/* Results Count */}
        <p className="text-center text-gray-600 mt-6">
          Showing {filteredProducts.length} of {products.length} products
        </p>
      </div>
    </div>
  );
}
