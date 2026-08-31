# আমার দোকান - Firebase Schema

## Collections Structure

### 1. **users**
```
{
  uid: string,
  phone: string,
  email: string,
  displayName: string,
  photoURL: string,
  shop_id: string,
  role: 'owner' | 'admin' | 'user',
  status: 'active' | 'blocked' | 'pending',
  created_at: timestamp,
  updated_at: timestamp,
  last_login: timestamp,
  is_super_admin: boolean
}
```

### 2. **shops**
```
{
  shop_id: string,
  owner_id: string,
  shop_name: string,
  owner_name: string,
  phone: string,
  address: string,
  business_category: string,
  logo_url: string,
  created_at: timestamp,
  updated_at: timestamp,
  status: 'active' | 'inactive',
  settings: {
    currency: string,
    timezone: string,
    language: string
  }
}
```

### 3. **devices**
```
{
  device_id: string,
  user_id: string,
  shop_id: string,
  device_name: string,
  platform: 'android' | 'ios' | 'web',
  app_version: string,
  fcm_token: string,
  status: 'active' | 'blocked' | 'pending',
  created_at: timestamp,
  last_seen_at: timestamp,
  blocked_at: timestamp,
  os_version: string
}
```

### 4. **login_requests**
```
{
  request_id: string,
  user_id: string,
  shop_id: string,
  device_id: string,
  phone: string,
  email: string,
  user_name: string,
  device_name: string,
  platform: string,
  app_version: string,
  fcm_token: string,
  request_type: 'new_user' | 'new_device',
  status: 'pending' | 'approved' | 'rejected' | 'blocked',
  created_at: timestamp,
  updated_at: timestamp,
  reviewed_by: string,
  reviewed_at: timestamp,
  rejection_reason: string
}
```

### 5. **admins**
```
{
  admin_id: string,
  shop_id: string,
  user_id: string,
  permissions: [
    'user_management',
    'login_requests',
    'payment_requests',
    'sms_requests',
    'credit_management',
    'reports',
    'settings'
  ],
  created_at: timestamp,
  created_by: string
}
```

### 6. **categories**
```
{
  category_id: string,
  shop_id: string,
  name: string,
  description: string,
  created_at: timestamp
}
```

### 7. **products**
```
{
  product_id: string,
  shop_id: string,
  name: string,
  code: string,
  category_id: string,
  unit: string,
  purchase_price: number,
  sale_price: number,
  current_stock: number,
  minimum_stock: number,
  supplier_id: string,
  description: string,
  created_at: timestamp,
  updated_at: timestamp
}
```

### 8. **stock_transactions**
```
{
  transaction_id: string,
  shop_id: string,
  product_id: string,
  type: 'in' | 'out' | 'adjustment',
  quantity: number,
  reference_id: string,
  notes: string,
  created_at: timestamp,
  created_by: string
}
```

### 9. **customers**
```
{
  customer_id: string,
  shop_id: string,
  name: string,
  phone: string,
  address: string,
  total_purchase: number,
  total_paid: number,
  current_due: number,
  created_at: timestamp,
  updated_at: timestamp
}
```

### 10. **customer_ledger**
```
{
  ledger_id: string,
  shop_id: string,
  customer_id: string,
  transaction_type: 'sale' | 'payment',
  amount: number,
  due_before: number,
  due_after: number,
  reference_id: string,
  notes: string,
  created_at: timestamp
}
```

### 11. **suppliers**
```
{
  supplier_id: string,
  shop_id: string,
  name: string,
  phone: string,
  address: string,
  total_purchase: number,
  total_paid: number,
  current_payable: number,
  created_at: timestamp,
  updated_at: timestamp
}
```

### 12. **sales**
```
{
  sale_id: string,
  shop_id: string,
  customer_id: string,
  total_amount: number,
  paid_amount: number,
  due_amount: number,
  discount: number,
  payment_method: string,
  status: 'completed' | 'pending',
  created_at: timestamp,
  created_by: string
}
```

### 13. **sale_items**
```
{
  item_id: string,
  shop_id: string,
  sale_id: string,
  product_id: string,
  quantity: number,
  unit_price: number,
  total_price: number
}
```

### 14. **sms_requests**
```
{
  request_id: string,
  shop_id: string,
  user_id: string,
  customer_id: string,
  customer_name: string,
  customer_phone: string,
  message: string,
  due_amount: number,
  request_type: 'due_reminder' | 'payment_received' | 'custom',
  status: 'pending' | 'sent' | 'failed' | 'rejected',
  created_at: timestamp,
  updated_at: timestamp,
  sent_at: timestamp,
  credit_deducted: number,
  reviewed_by: string,
  reviewed_at: timestamp,
  rejection_reason: string,
  failure_reason: string
}
```

### 15. **sms_permissions**
```
{
  permission_id: string,
  shop_id: string,
  user_id: string,
  sms_enabled: boolean,
  updated_at: timestamp,
  updated_by: string
}
```

### 16. **sms_templates**
```
{
  template_id: string,
  shop_id: string,
  name: string,
  type: 'due_reminder' | 'payment_received',
  content: string,
  variables: [string],
  is_default: boolean,
  created_at: timestamp,
  updated_at: timestamp
}
```

### 17. **credit_packages**
```
{
  package_id: string,
  shop_id: string,
  credit_amount: number,
  price: number,
  description: string,
  is_active: boolean,
  sort_order: number,
  created_at: timestamp,
  updated_at: timestamp
}
```

### 18. **credit_wallet**
```
{
  wallet_id: string,
  shop_id: string,
  user_id: string,
  balance: number,
  total_purchased: number,
  total_used: number,
  updated_at: timestamp
}
```

### 19. **credit_transactions**
```
{
  transaction_id: string,
  shop_id: string,
  user_id: string,
  type: 'purchase' | 'use' | 'loan' | 'refund',
  amount: number,
  balance_before: number,
  balance_after: number,
  reference_id: string,
  notes: string,
  created_at: timestamp
}
```

### 20. **payment_requests**
```
{
  request_id: string,
  shop_id: string,
  user_id: string,
  package_id: string,
  credit_amount: number,
  amount: number,
  payment_method_id: string,
  transaction_id: string,
  status: 'pending' | 'approved' | 'rejected',
  created_at: timestamp,
  reviewed_at: timestamp,
  reviewed_by: string,
  rejection_reason: string
}
```

### 21. **payment_methods**
```
{
  method_id: string,
  shop_id: string,
  name: string,
  account_number: string,
  merchant_number: string,
  instructions: string,
  icon_url: string,
  is_active: boolean,
  sort_order: number,
  created_at: timestamp,
  updated_at: timestamp
}
```

### 22. **loan_transactions**
```
{
  loan_id: string,
  shop_id: string,
  user_id: string,
  credit_amount: number,
  status: 'active' | 'repaid',
  created_at: timestamp,
  repaid_at: timestamp
}
```

### 23. **notifications**
```
{
  notification_id: string,
  shop_id: string,
  recipient_id: string,
  recipient_role: string,
  type: 'login_request' | 'payment_request' | 'sms_request' | 'system_alert',
  title: string,
  body: string,
  reference_id: string,
  is_read: boolean,
  created_at: timestamp,
  read_at: timestamp
}
```

### 24. **settings**
```
{
  setting_id: string,
  shop_id: string,
  key: string,
  value: string,
  updated_at: timestamp,
  updated_by: string
}
```

### 25. **app_versions**
```
{
  version_id: string,
  version_number: string,
  platform: 'android' | 'ios' | 'web',
  is_latest: boolean,
  is_minimum: boolean,
  force_update: boolean,
  update_message: string,
  download_link: string,
  released_at: timestamp
}
```

## Indexes Required

- `users`: shop_id, email, phone
- `devices`: user_id, shop_id, fcm_token
- `login_requests`: shop_id, status, created_at
- `customers`: shop_id, name, phone
- `sales`: shop_id, customer_id, created_at
- `sms_requests`: shop_id, user_id, status, created_at
- `payment_requests`: shop_id, user_id, status, created_at
- `credit_transactions`: shop_id, user_id, created_at
- `notifications`: shop_id, recipient_id, is_read, created_at

## Multi-Tenant Security Rules

All collections must include `shop_id` for tenant isolation.
All queries must filter by authenticated user's shop_id.
