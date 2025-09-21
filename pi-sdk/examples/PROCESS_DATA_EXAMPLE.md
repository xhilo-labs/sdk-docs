# Payment Process Data Example

This example shows how to use the payment process data to provide real-time feedback to users during Pi payments.

## Basic Usage with Process Data

```typescript
import React, { useState } from 'react';
import { usePiPayments, PaymentProcessDisplay } from '@xhilo/pi-sdk/react';

function PaymentWithProcessData({ userId }: { userId: string }) {
  const { createAndCompletePayment, isProcessing, error, success } = usePiPayments();
  const [processData, setProcessData] = useState(null);

  const handlePayment = async () => {
    const result = await createAndCompletePayment({
      userId,
      amount: 1.0,
      memo: 'Test payment with process data',
      metadata: { orderId: 'ORD-123' },
      onProcessUpdate: (data) => {
        setProcessData(data);
        console.log('Process update:', data);
      },
      onSuccess: (paymentId) => {
        console.log('Payment completed:', paymentId);
      },
      onError: (error) => {
        console.error('Payment failed:', error);
      }
    });
  };

  return (
    <div className="max-w-md mx-auto p-4">
      <h2 className="text-xl font-bold mb-4">Pi Payment with Process Data</h2>
      
      <button
        onClick={handlePayment}
        disabled={isProcessing}
        className="bg-blue-600 text-white px-6 py-3 rounded-lg disabled:opacity-50"
      >
        {isProcessing ? 'Processing...' : 'Pay 1 Pi'}
      </button>

      {/* Display process data */}
      <PaymentProcessDisplay
        processData={processData}
        showProgress={true}
        showTimestamp={true}
        className="mt-4 p-4 bg-gray-50 rounded-lg"
      />

      {error && (
        <div className="mt-4 p-3 bg-red-100 text-red-700 rounded">
          Error: {error}
        </div>
      )}

      {success && (
        <div className="mt-4 p-3 bg-green-100 text-green-700 rounded">
          Payment completed successfully!
        </div>
      )}
    </div>
  );
}
```

## Advanced Process Data Handling

```typescript
import React, { useState, useEffect } from 'react';
import { usePiPayments, PaymentProcessData } from '@xhilo/pi-sdk/react';

function AdvancedPaymentComponent({ userId }: { userId: string }) {
  const { createAndCompletePayment } = usePiPayments();
  const [processData, setProcessData] = useState<PaymentProcessData | null>(null);
  const [processHistory, setProcessHistory] = useState<PaymentProcessData[]>([]);

  const handleProcessUpdate = (data: PaymentProcessData) => {
    setProcessData(data);
    setProcessHistory(prev => [...prev, data]);
    
    // Custom handling based on stage
    switch (data.stage) {
      case 'initiating':
        console.log('Payment initiated');
        break;
      case 'approving':
        console.log('Waiting for approval...');
        break;
      case 'completing':
        console.log('Completing payment...');
        break;
      case 'success':
        console.log('Payment successful!');
        // Show success animation, update UI, etc.
        break;
      case 'error':
        console.error('Payment failed:', data.message);
        // Show error message, retry option, etc.
        break;
      case 'cancelled':
        console.log('Payment cancelled');
        // Reset form, show cancellation message, etc.
        break;
    }
  };

  const handlePayment = async () => {
    setProcessHistory([]); // Clear previous history
    
    const result = await createAndCompletePayment({
      userId,
      amount: 2.5,
      memo: 'Advanced payment example',
      metadata: { 
        orderId: 'ORD-' + Date.now(),
        feature: 'premium'
      },
      onProcessUpdate: handleProcessUpdate,
      onSuccess: (paymentId) => {
        console.log('Payment completed with ID:', paymentId);
        // Update user's premium status, etc.
      },
      onError: (error) => {
        console.error('Payment error:', error);
        // Show error notification
      }
    });
  };

  return (
    <div className="max-w-2xl mx-auto p-6">
      <h2 className="text-2xl font-bold mb-6">Advanced Payment with Process Tracking</h2>
      
      <button
        onClick={handlePayment}
        disabled={processData?.stage === 'initiating' || processData?.stage === 'approving' || processData?.stage === 'completing'}
        className="bg-green-600 text-white px-8 py-4 rounded-lg text-lg font-semibold disabled:opacity-50 disabled:cursor-not-allowed"
      >
        {processData?.stage === 'initiating' && 'Initiating...'}
        {processData?.stage === 'approving' && 'Approving...'}
        {processData?.stage === 'completing' && 'Completing...'}
        {!processData && 'Pay 2.5 Pi'}
      </button>

      {/* Current process status */}
      {processData && (
        <div className="mt-6 p-4 bg-blue-50 rounded-lg">
          <h3 className="font-semibold text-blue-800 mb-2">Current Status</h3>
          <PaymentProcessDisplay
            processData={processData}
            showProgress={true}
            showTimestamp={true}
          />
        </div>
      )}

      {/* Process history */}
      {processHistory.length > 0 && (
        <div className="mt-6">
          <h3 className="font-semibold text-gray-800 mb-3">Process History</h3>
          <div className="space-y-2">
            {processHistory.map((step, index) => (
              <div key={index} className="flex items-center space-x-3 p-2 bg-gray-50 rounded">
                <span className="text-sm text-gray-500">
                  {new Date(step.timestamp).toLocaleTimeString()}
                </span>
                <span className="text-sm font-medium">{step.message}</span>
                {step.progress && (
                  <span className="text-xs text-gray-500">({step.progress}%)</span>
                )}
              </div>
            ))}
          </div>
        </div>
      )}
    </div>
  );
}
```

## Custom Process Display Component

```typescript
import React from 'react';
import { PaymentProcessData } from '@xhilo/pi-sdk/react';

function CustomProcessDisplay({ processData }: { processData: PaymentProcessData | null }) {
  if (!processData) return null;

  const getStageInfo = (stage: PaymentProcessData['stage']) => {
    switch (stage) {
      case 'initiating':
        return { 
          icon: '🚀', 
          color: 'blue', 
          title: 'Starting Payment',
          description: 'Initializing payment process...'
        };
      case 'approving':
        return { 
          icon: '⏳', 
          color: 'yellow', 
          title: 'Waiting for Approval',
          description: 'Your payment is being reviewed...'
        };
      case 'completing':
        return { 
          icon: '⚡', 
          color: 'purple', 
          title: 'Finalizing Payment',
          description: 'Completing the transaction...'
        };
      case 'success':
        return { 
          icon: '✅', 
          color: 'green', 
          title: 'Payment Successful',
          description: 'Your payment has been completed!'
        };
      case 'error':
        return { 
          icon: '❌', 
          color: 'red', 
          title: 'Payment Failed',
          description: 'There was an error processing your payment.'
        };
      case 'cancelled':
        return { 
          icon: '🚫', 
          color: 'gray', 
          title: 'Payment Cancelled',
          description: 'The payment was cancelled.'
        };
    }
  };

  const stageInfo = getStageInfo(processData.stage);

  return (
    <div className={`p-6 rounded-lg border-2 border-${stageInfo.color}-200 bg-${stageInfo.color}-50`}>
      <div className="flex items-center space-x-4">
        <div className="text-4xl">{stageInfo.icon}</div>
        <div className="flex-1">
          <h3 className={`text-lg font-semibold text-${stageInfo.color}-800`}>
            {stageInfo.title}
          </h3>
          <p className={`text-${stageInfo.color}-600`}>
            {stageInfo.description}
          </p>
          {processData.paymentId && (
            <p className="text-sm text-gray-600 mt-1">
              Payment ID: {processData.paymentId}
            </p>
          )}
        </div>
      </div>
      
      {processData.progress !== undefined && (
        <div className="mt-4">
          <div className="flex justify-between text-sm text-gray-600 mb-2">
            <span>Progress</span>
            <span>{processData.progress}%</span>
          </div>
          <div className="w-full bg-gray-200 rounded-full h-3">
            <div
              className={`h-3 rounded-full transition-all duration-500 bg-${stageInfo.color}-500`}
              style={{ width: `${processData.progress}%` }}
            />
          </div>
        </div>
      )}
    </div>
  );
}
```

## Process Data Types

```typescript
interface PaymentProcessData {
  stage: 'initiating' | 'approving' | 'completing' | 'success' | 'error' | 'cancelled';
  message: string;
  paymentId?: string;
  txid?: string;
  progress?: number; // 0-100
  timestamp: string;
}
```

## Key Benefits

1. **Real-time Feedback**: Users see exactly what's happening during the payment process
2. **Progress Tracking**: Visual progress bars and status updates
3. **Error Handling**: Clear error messages and recovery options
4. **Customizable UI**: Easy to style and customize the process display
5. **Debugging**: Process history helps with troubleshooting
6. **User Experience**: Reduces anxiety by showing payment progress

This process data system ensures users always know what's happening with their payments and provides a much better user experience.
