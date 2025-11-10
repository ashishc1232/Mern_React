import React from 'react';

// Reusable Button Component
const Button = ({ 
  children, 
  onClick, 
  variant = 'primary', 
  size = 'medium',
  disabled = false 
}) => {
  const baseStyles = 'font-semibold rounded-lg transition-all duration-200';
  
  const variants = {
    primary: 'bg-blue-600 hover:bg-blue-700 text-white',
    secondary: 'bg-gray-200 hover:bg-gray-300 text-gray-800',
    success: 'bg-green-600 hover:bg-green-700 text-white',
  };
  
  const sizes = {
    small: 'px-3 py-1 text-sm',
    medium: 'px-4 py-2 text-base',
    large: 'px-6 py-3 text-lg',
  };
  
  return (
    <button
      onClick={onClick}
      disabled={disabled}
      className={`${baseStyles} ${variants[variant]} ${sizes[size]} ${
        disabled ? 'opacity-50 cursor-not-allowed' : ''
      }`}
    >
      {children}
    </button>
  );
};

// Reusable Card Component
const Card = ({ 
  children, 
  className = '', 
  shadow = true,
  padding = 'medium' 
}) => {
  const paddings = {
    small: 'p-3',
    medium: 'p-4',
    large: 'p-6',
  };
  
  return (
    <div 
      className={`bg-white rounded-lg ${shadow ? 'shadow-md hover:shadow-lg' : ''} 
        ${paddings[padding]} transition-shadow duration-200 ${className}`}
    >
      {children}
    </div>
  );
};

// Reusable Header Component
const Header = ({ 
  title, 
  subtitle, 
  level = 1,
  className = '' 
}) => {
  const levels = {
    1: 'text-3xl font-bold',
    2: 'text-2xl font-bold',
    3: 'text-xl font-semibold',
  };
  
  const HeadingTag = `h${level}`;
  
  return (
    <div className={`mb-4 ${className}`}>
      <HeadingTag className={levels[level]}>{title}</HeadingTag>
      {subtitle && <p className="text-gray-600 mt-1">{subtitle}</p>}
    </div>
  );
};

// Product Card Component (combines all reusable components)
const ProductCard = ({ 
  image, 
  title, 
  price, 
  description,
  onAddToCart,
  onViewDetails 
}) => {
  return (
    <Card className="max-w-sm">
      <img 
        src={image} 
        alt={title}
        className="w-full h-48 object-cover rounded-md mb-4"
      />
      
      <Header title={title} level={3} />
      
      {description && (
        <p className="text-gray-600 text-sm mb-4 line-clamp-2">
          {description}
        </p>
      )}
      
      <div className="flex items-center justify-between mb-4">
        <span className="text-2xl font-bold text-blue-600">${price}</span>
      </div>
      
      <div className="flex gap-2">
        <Button 
          variant="primary" 
          onClick={onAddToCart}
          className="flex-1"
        >
          Add to Cart
        </Button>
        <Button 
          variant="secondary" 
          onClick={onViewDetails}
        >
          Details
        </Button>
      </div>
    </Card>
  );
};

// Demo App
export default function App() {
  const products = [
    {
      id: 1,
      image: 'https://images.unsplash.com/photo-1505740420928-5e560c06d30e?w=400&h=300&fit=crop',
      title: 'Wireless Headphones',
      price: 89.99,
      description: 'Premium sound quality with active noise cancellation and 30-hour battery life.'
    },
    {
      id: 2,
      image: 'https://images.unsplash.com/photo-1523275335684-37898b6baf30?w=400&h=300&fit=crop',
      title: 'Smart Watch',
      price: 249.99,
      description: 'Track your fitness goals with advanced health monitoring and GPS.'
    },
    {
      id: 3,
      image: 'https://images.unsplash.com/photo-1572635196237-14b3f281503f?w=400&h=300&fit=crop',
      title: 'Sunglasses',
      price: 129.99,
      description: 'UV protection with polarized lenses and modern design.'
    }
  ];
  
  const handleAddToCart = (product) => {
    alert(`Added ${product.title} to cart!`);
  };
  
  const handleViewDetails = (product) => {
    alert(`Viewing details for ${product.title}`);
  };
  
  return (
    <div className="min-h-screen bg-gradient-to-br from-gray-50 to-gray-100 p-8">
      <div className="max-w-6xl mx-auto">
        <Header 
          title="Component Demo Store" 
          subtitle="Reusable React components with props destructuring"
          level={1}
          className="text-center mb-12"
        />
        
        <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
          {products.map(product => (
            <ProductCard
              key={product.id}
              image={product.image}
              title={product.title}
              price={product.price}
              description={product.description}
              onAddToCart={() => handleAddToCart(product)}
              onViewDetails={() => handleViewDetails(product)}
            />
          ))}
        </div>
        
        {/* Example of using individual components */}
        <Card className="mt-12" padding="large">
          <Header 
            title="About Our Components" 
            subtitle="Learn how these reusable components work"
            level={2}
          />
          <p className="text-gray-700 mb-4">
            This demo showcases reusable React components with proper props destructuring:
          </p>
          <ul className="list-disc list-inside text-gray-700 space-y-2 mb-4">
            <li><strong>Button:</strong> Customizable variants, sizes, and states</li>
            <li><strong>Card:</strong> Flexible container with shadow and padding options</li>
            <li><strong>Header:</strong> Configurable heading levels with optional subtitles</li>
            <li><strong>ProductCard:</strong> Composite component using all the above</li>
          </ul>
          <Button variant="success" onClick={() => alert('You clicked the success button!')}>
            Learn More
          </Button>
        </Card>
      </div>
    </div>
  );
}
