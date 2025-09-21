---
layout: default
title: Examples
---

# Examples

Real-world examples and use cases for the Pi SDK.

## 🛒 E-commerce Integration

### Shopping Cart with Pi Payments

```tsx
import { usePiPayments } from '@xhilo/pi-sdk/react'
import { useState } from 'react'

function ShoppingCart() {
  const { createPayment, approvePayment, completePayment } = usePiPayments()
  const [cart, setCart] = useState([])
  const [isProcessing, setIsProcessing] = useState(false)

  const calculateTotal = () => {
    return cart.reduce((total, item) => total + item.price, 0)
  }

  const handleCheckout = async () => {
    setIsProcessing(true)
    
    try {
      const total = calculateTotal()
      const payment = await createPayment({
        amount: total,
        memo: `Purchase of ${cart.length} items`,
        metadata: {
          items: cart.map(item => ({
            id: item.id,
            name: item.name,
            price: item.price
          }))
        }
      })

      const approved = await approvePayment(payment.identifier)
      
      if (approved) {
        const completed = await completePayment(payment.identifier)
        
        if (completed) {
          // Clear cart and show success
          setCart([])
          alert('Payment successful!')
        }
      }
    } catch (error) {
      console.error('Payment failed:', error)
      alert('Payment failed. Please try again.')
    } finally {
      setIsProcessing(false)
    }
  }

  return (
    <div className="shopping-cart">
      <h2>Shopping Cart</h2>
      
      {cart.map(item => (
        <div key={item.id} className="cart-item">
          <span>{item.name}</span>
          <span>${item.price}</span>
        </div>
      ))}
      
      <div className="total">
        Total: ${calculateTotal()}
      </div>
      
      <button 
        onClick={handleCheckout}
        disabled={isProcessing || cart.length === 0}
        className="checkout-btn"
      >
        {isProcessing ? 'Processing...' : 'Pay with Pi'}
      </button>
    </div>
  )
}
```

## 🎮 Gaming Integration

### In-Game Purchases

```tsx
import { usePiSimplePayments } from '@xhilo/pi-sdk/react'
import { useState } from 'react'

function GameShop() {
  const { createPayment } = usePiSimplePayments()
  const [userCoins, setUserCoins] = useState(100)

  const gameItems = [
    { id: 1, name: 'Health Potion', price: 5, type: 'consumable' },
    { id: 2, name: 'Magic Sword', price: 25, type: 'weapon' },
    { id: 3, name: 'Shield', price: 20, type: 'armor' },
    { id: 4, name: 'Gold Pack', price: 10, type: 'currency' }
  ]

  const handlePurchase = async (item) => {
    try {
      const payment = await createPayment({
        amount: item.price,
        memo: `Purchase: ${item.name}`,
        metadata: {
          itemId: item.id,
          itemType: item.type,
          gameSession: 'current-session-id'
        }
      })

      // Add item to user's inventory
      setUserCoins(prev => prev - item.price)
      alert(`Purchased ${item.name}!`)
      
    } catch (error) {
      console.error('Purchase failed:', error)
      alert('Purchase failed. Please try again.')
    }
  }

  return (
    <div className="game-shop">
      <h2>Game Shop</h2>
      <div className="coins">Coins: {userCoins}</div>
      
      <div className="items">
        {gameItems.map(item => (
          <div key={item.id} className="shop-item">
            <h3>{item.name}</h3>
            <p>Price: {item.price} Pi</p>
            <button 
              onClick={() => handlePurchase(item)}
              disabled={userCoins < item.price}
            >
              Buy
            </button>
          </div>
        ))}
      </div>
    </div>
  )
}
```

## 📱 Mobile App Integration

### React Native with Pi SDK

```tsx
import { useXhiloPiUser, usePiSimplePayments } from '@xhilo/pi-sdk/react'
import { View, Text, TouchableOpacity, Alert } from 'react-native'

function MobilePaymentScreen() {
  const user = useXhiloPiUser()
  const { createPayment } = usePiSimplePayments()

  const handleTip = async (amount) => {
    if (!user) {
      Alert.alert('Error', 'Please log in first')
      return
    }

    try {
      const payment = await createPayment({
        amount: amount,
        memo: `Tip for ${user.username}`,
        metadata: {
          type: 'tip',
          recipient: user.uid
        }
      })

      Alert.alert('Success', 'Tip sent successfully!')
      
    } catch (error) {
      Alert.alert('Error', 'Failed to send tip')
    }
  }

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Send Tip</Text>
      
      {user ? (
        <View>
          <Text>Welcome, {user.username}!</Text>
          <Text>Wallet: {user.walletAddress}</Text>
          
          <View style={styles.tipButtons}>
            <TouchableOpacity 
              style={styles.tipButton}
              onPress={() => handleTip(1)}
            >
              <Text>Tip 1 Pi</Text>
            </TouchableOpacity>
            
            <TouchableOpacity 
              style={styles.tipButton}
              onPress={() => handleTip(5)}
            >
              <Text>Tip 5 Pi</Text>
            </TouchableOpacity>
            
            <TouchableOpacity 
              style={styles.tipButton}
              onPress={() => handleTip(10)}
            >
              <Text>Tip 10 Pi</Text>
            </TouchableOpacity>
          </View>
        </View>
      ) : (
        <Text>Please log in to send tips</Text>
      )}
    </View>
  )
}
```

## 🎨 Creative Platform

### Digital Art Marketplace

```tsx
import { usePiPayments } from '@xhilo/pi-sdk/react'
import { useState, useEffect } from 'react'

function ArtMarketplace() {
  const { createPayment, approvePayment, completePayment } = usePiPayments()
  const [artworks, setArtworks] = useState([])
  const [purchased, setPurchased] = useState([])

  useEffect(() => {
    // Fetch artworks from your API
    fetchArtworks()
  }, [])

  const handlePurchaseArtwork = async (artwork) => {
    try {
      const payment = await createPayment({
        amount: artwork.price,
        memo: `Purchase: ${artwork.title}`,
        metadata: {
          artworkId: artwork.id,
          artistId: artwork.artistId,
          title: artwork.title,
          type: 'digital_art'
        }
      })

      const approved = await approvePayment(payment.identifier)
      
      if (approved) {
        const completed = await completePayment(payment.identifier)
        
        if (completed) {
          // Add to purchased items
          setPurchased(prev => [...prev, artwork])
          
          // Download artwork (implement your download logic)
          downloadArtwork(artwork)
          
          alert('Artwork purchased successfully!')
        }
      }
    } catch (error) {
      console.error('Purchase failed:', error)
      alert('Purchase failed. Please try again.')
    }
  }

  return (
    <div className="art-marketplace">
      <h1>Digital Art Marketplace</h1>
      
      <div className="artworks-grid">
        {artworks.map(artwork => (
          <div key={artwork.id} className="artwork-card">
            <img src={artwork.imageUrl} alt={artwork.title} />
            <h3>{artwork.title}</h3>
            <p>by {artwork.artistName}</p>
            <p className="price">{artwork.price} Pi</p>
            
            {purchased.find(item => item.id === artwork.id) ? (
              <button disabled>Purchased</button>
            ) : (
              <button onClick={() => handlePurchaseArtwork(artwork)}>
                Buy with Pi
              </button>
            )}
          </div>
        ))}
      </div>
    </div>
  )
}
```

## 🎵 Music Streaming

### Premium Subscription

```tsx
import { usePiPayments } from '@xhilo/pi-sdk/react'
import { useState } from 'react'

function SubscriptionPlans() {
  const { createPayment, approvePayment, completePayment } = usePiPayments()
  const [subscription, setSubscription] = useState(null)

  const plans = [
    { id: 'basic', name: 'Basic', price: 5, features: ['Ad-free', 'High quality'] },
    { id: 'premium', name: 'Premium', price: 10, features: ['Ad-free', 'High quality', 'Offline downloads'] },
    { id: 'pro', name: 'Pro', price: 20, features: ['Ad-free', 'High quality', 'Offline downloads', 'Exclusive content'] }
  ]

  const handleSubscribe = async (plan) => {
    try {
      const payment = await createPayment({
        amount: plan.price,
        memo: `Monthly subscription: ${plan.name}`,
        metadata: {
          planId: plan.id,
          planName: plan.name,
          billingCycle: 'monthly',
          features: plan.features
        }
      })

      const approved = await approvePayment(payment.identifier)
      
      if (approved) {
        const completed = await completePayment(payment.identifier)
        
        if (completed) {
          setSubscription(plan)
          alert(`Subscribed to ${plan.name} plan!`)
        }
      }
    } catch (error) {
      console.error('Subscription failed:', error)
      alert('Subscription failed. Please try again.')
    }
  }

  return (
    <div className="subscription-plans">
      <h2>Choose Your Plan</h2>
      
      <div className="plans">
        {plans.map(plan => (
          <div key={plan.id} className="plan-card">
            <h3>{plan.name}</h3>
            <div className="price">{plan.price} Pi/month</div>
            
            <ul className="features">
              {plan.features.map((feature, index) => (
                <li key={index}>{feature}</li>
              ))}
            </ul>
            
            <button 
              onClick={() => handleSubscribe(plan)}
              className="subscribe-btn"
            >
              Subscribe
            </button>
          </div>
        ))}
      </div>
      
      {subscription && (
        <div className="current-subscription">
          <h3>Current Plan: {subscription.name}</h3>
          <p>Next billing: Next month</p>
        </div>
      )}
    </div>
  )
}
```

## 🔧 Backend Integration Examples

### Express.js API

```javascript
import express from 'express'
import { PiBackendClient } from '@xhilo/pi-sdk/backend'

const app = express()
const piClient = new PiBackendClient({
  apiKey: process.env.PI_API_KEY,
  walletPrivateSeed: process.env.PI_WALLET_PRIVATE_SEED,
  sandbox: process.env.NODE_ENV !== 'production'
})

// Create payment endpoint
app.post('/api/payments/create', async (req, res) => {
  try {
    const { amount, memo, metadata } = req.body
    
    const payment = await piClient.createPayment({
      amount,
      memo,
      metadata
    })
    
    res.json({ success: true, payment })
  } catch (error) {
    res.status(500).json({ error: error.message })
  }
})

// Approve payment endpoint
app.post('/api/payments/approve', async (req, res) => {
  try {
    const { paymentIdentifier } = req.body
    
    const approved = await piClient.approvePayment(paymentIdentifier)
    
    res.json({ success: true, approved })
  } catch (error) {
    res.status(500).json({ error: error.message })
  }
})

// Complete payment endpoint
app.post('/api/payments/complete', async (req, res) => {
  try {
    const { paymentIdentifier } = req.body
    
    const completed = await piClient.completePayment(paymentIdentifier)
    
    res.json({ success: true, payment: completed })
  } catch (error) {
    res.status(500).json({ error: error.message })
  }
})

app.listen(3001, () => {
  console.log('Server running on port 3001')
})
```

## 🎯 Best Practices

### Error Handling

```tsx
const handlePayment = async () => {
  try {
    const payment = await createPayment({
      amount: 1.0,
      memo: 'Test payment'
    })
    
    // Handle success
    console.log('Payment created:', payment)
    
  } catch (error) {
    // Handle different error types
    if (error.name === 'AuthenticationError') {
      alert('Please log in first')
    } else if (error.name === 'PaymentError') {
      alert('Payment failed. Please try again.')
    } else {
      alert('An unexpected error occurred')
    }
  }
}
```

### Loading States

```tsx
const [isLoading, setIsLoading] = useState(false)

const handlePayment = async () => {
  setIsLoading(true)
  
  try {
    const payment = await createPayment({
      amount: 1.0,
      memo: 'Test payment'
    })
    
    // Handle success
    
  } catch (error) {
    // Handle error
    
  } finally {
    setIsLoading(false)
  }
}

return (
  <button onClick={handlePayment} disabled={isLoading}>
    {isLoading ? 'Processing...' : 'Pay with Pi'}
  </button>
)
```

### User Feedback

```tsx
const [message, setMessage] = useState('')

const handlePayment = async () => {
  setMessage('Creating payment...')
  
  try {
    const payment = await createPayment({
      amount: 1.0,
      memo: 'Test payment'
    })
    
    setMessage('Payment created! Please approve...')
    
    const approved = await approvePayment(payment.identifier)
    
    if (approved) {
      setMessage('Payment approved! Completing...')
      
      const completed = await completePayment(payment.identifier)
      
      setMessage('Payment completed successfully!')
    }
    
  } catch (error) {
    setMessage(`Error: ${error.message}`)
  }
}

return (
  <div>
    <button onClick={handlePayment}>Pay with Pi</button>
    {message && <div className="message">{message}</div>}
  </div>
)
```
