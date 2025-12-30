# Receipt Template Guide

## Overview

The receipt template system allows you to create formatted receipts with proper alignment, styling, and structure. This guide explains how to use nested sections and all available features.

---

## Template Structure

### Basic Structure

```json
{
  "section_name": {
    "field_name": {
      "align": "left|center|right",
      "bold": true|false,
      "prefix": "Text before value",
      "suffix": "Text after value",
      "format": "Number or date format",
      "width": 42,
      "text": "Static text"
    }
  }
}
```

---

## Nested Sections

Nested sections allow you to group related fields together. The formatter automatically handles nested objects.

### Example: Header Section

```json
{
  "header": {
    "store_name": {
      "align": "center",
      "bold": true,
      "width": 42
    },
    "store_address": {
      "align": "center",
      "width": 42
    },
    "store_phone": {
      "align": "center",
      "width": 42
    }
  }
}
```

Data:
```json
{
  "store_name": "My Store",
  "store_address": "123 Main St",
  "store_phone": "+1 234-567-8900"
}
```

Output:
```
              My Store              
          123 Main St          
        +1 234-567-8900        
```

---

## Field Types

### 1. Simple Text Field

Display a value from data:

```json
{
  "customer_name": {
    "prefix": "Customer: ",
    "align": "left",
    "width": 42
  }
}
```

### 2. Static Text

Display fixed text:

```json
{
  "title": {
    "text": "INVOICE",
    "align": "center",
    "bold": true,
    "width": 42
  }
}
```

### 3. Formatted Number

```json
{
  "total": {
    "prefix": "Total: $",
    "format": ".2f",
    "align": "right",
    "width": 42
  }
}
```

### 4. Formatted Date

```json
{
  "date": {
    "prefix": "Date: ",
    "format": "MM/DD/YYYY HH:mm",
    "align": "left",
    "width": 42
  }
}
```

### 5. Divider

```json
{
  "divider1": {
    "char": "=",
    "length": 42
  }
}
```

### 6. Table

```json
{
  "items": {
    "header": {
      "columns": [
        {"text": "Item", "width": 22, "align": "left"},
        {"text": "Qty", "width": 6, "align": "center"},
        {"text": "Price", "width": 14, "align": "right"}
      ],
      "divider": true
    },
    "row": {
      "name": {"width": 22, "align": "left"},
      "quantity": {"width": 6, "align": "center"},
      "price": {"width": 14, "align": "right", "format": ".2f"}
    }
  }
}
```

---

## Complete Example

### Template

```json
{
  "header": {
    "store_name": {"align": "center", "bold": true, "width": 42},
    "store_address": {"align": "center", "width": 42},
    "store_phone": {"align": "center", "width": 42}
  },
  "divider1": {"char": "=", "length": 42},
  "title": {"text": "BILL", "align": "center", "bold": true, "width": 42},
  "divider2": {"char": "=", "length": 42},
  "order_info": {
    "order_number": {"prefix": "Order #: ", "align": "left", "width": 42},
    "date": {"prefix": "Date: ", "align": "left", "width": 42},
    "time": {"prefix": "Time: ", "align": "left", "width": 42}
  },
  "divider3": {"char": "-", "length": 42},
  "customer_info": {
    "customer": {"prefix": "Customer: ", "align": "left", "width": 42},
    "phone": {"prefix": "Phone: ", "align": "left", "width": 42},
    "delivery_address": {"prefix": "Delivery: ", "align": "left", "width": 42}
  },
  "divider4": {"char": "-", "length": 42},
  "items": {
    "header": {
      "columns": [
        {"text": "Item", "width": 22, "align": "left"},
        {"text": "Qty", "width": 6, "align": "center"},
        {"text": "Price", "width": 14, "align": "right"}
      ],
      "divider": true
    },
    "row": {
      "name": {"width": 22, "align": "left"},
      "quantity": {"width": 6, "align": "center"},
      "price": {"width": 14, "align": "right", "format": ".2f"}
    }
  },
  "divider5": {"char": "-", "length": 42},
  "pricing": {
    "subtotal": {"prefix": "Subtotal: ", "format": ".2f", "align": "right", "width": 42},
    "delivery": {"prefix": "Delivery: ", "format": ".2f", "align": "right", "width": 42},
    "total": {"prefix": "TOTAL: ", "format": ".2f", "align": "right", "width": 42, "bold": true}
  },
  "divider6": {"char": "=", "length": 42},
  "payment": {"text": "Cash Payment", "align": "center", "bold": true, "width": 42},
  "footer": {
    "thank_you": {"text": "Thank you for your order!", "align": "center", "width": 42},
    "visit_again": {"text": "Please visit again", "align": "center", "width": 42},
    "divider": {"char": "=", "length": 42},
    "copy_type": {"text": "*** CUSTOMER COPY ***", "align": "center", "bold": true, "width": 42},
    "print_time": {"prefix": "Printed: ", "align": "center", "format": "MM/DD/YYYY HH:mm", "width": 42}
  }
}
```

### Data

```json
{
  "store_name": "CallEatMandu",
  "store_address": "Sankhamul Planning, Lalitpur",
  "store_phone": "+977 9801254660",
  "order_number": "ORD-250905-8473",
  "date": "2025-09-05",
  "time": "07:33:00",
  "customer": "cc",
  "phone": "9701343591",
  "delivery_address": "Buddhanagar",
  "items": [
    {"name": "Chicken Biryani", "quantity": 1, "price": 449.00},
    {"name": "Hot Wings 4 pcs", "quantity": 1, "price": 299.00},
    {"name": "Mineral Water", "quantity": 1, "price": 30.00}
  ],
  "subtotal": 778.00,
  "delivery": 100.00,
  "total": 878.00,
  "payment_method": "Cash Payment",
  "print_time": "2025-09-05T07:33:57"
}
```

### Output

```


            CallEatMandu            
     Sankhamul Planning, Lalitpur     
         +977 9801254660         
==========================================
                 BILL                 
==========================================
Order #: ORD-250905-8473              
Date: 2025-09-05                      
Time: 07:33:00                        
------------------------------------------
Customer: cc                          
Phone: 9701343591                     
Delivery: Buddhanagar                 
------------------------------------------
Item                  Qty        Price
------------------------------------------
Chicken Biryani        1        449.00
Hot Wings 4 pcs        1        299.00
Mineral Water          1         30.00
------------------------------------------
                    Subtotal: 778.00
                    Delivery: 100.00
                       TOTAL: 878.00
==========================================
            Cash Payment            
     Thank you for your order!     
        Please visit again        
==========================================
       *** CUSTOMER COPY ***       
    Printed: 09/05/2025 07:33    




```

---

## Field Properties

### align
- **Values**: `left`, `center`, `right`
- **Default**: `left`
- **Description**: Text alignment within the field width

### bold
- **Values**: `true`, `false`
- **Default**: `false`
- **Description**: Makes text bold (ESC/POS command)

### prefix
- **Type**: String
- **Description**: Text to display before the value

### suffix
- **Type**: String
- **Description**: Text to display after the value

### format
- **For Numbers**: `.2f` (2 decimals), `.0f` (no decimals)
- **For Dates**: `MM/DD/YYYY`, `HH:mm:ss`, etc.
- **Description**: Format for numbers or dates

### width
- **Type**: Integer
- **Default**: 42 (paper width)
- **Description**: Total width of the field including padding

### text
- **Type**: String
- **Description**: Static text (when no data value needed)

---

## Date Formatting

### Available Tokens

- `YYYY` - Full year (2025)
- `YY` - Short year (25)
- `MM` - Month with leading zero (01-12)
- `M` - Month without leading zero (1-12)
- `DD` - Day with leading zero (01-31)
- `D` - Day without leading zero (1-31)
- `HH` - Hour 24-hour with leading zero (00-23)
- `H` - Hour 24-hour without leading zero (0-23)
- `hh` - Hour 12-hour with leading zero (01-12)
- `h` - Hour 12-hour without leading zero (1-12)
- `mm` - Minutes with leading zero (00-59)
- `m` - Minutes without leading zero (0-59)
- `ss` - Seconds with leading zero (00-59)
- `s` - Seconds without leading zero (0-59)
- `A` - AM/PM uppercase
- `a` - am/pm lowercase

### Examples

```json
{
  "date1": {"format": "MM/DD/YYYY"},           // 09/05/2025
  "date2": {"format": "DD-MM-YYYY"},           // 05-09-2025
  "date3": {"format": "YYYY-MM-DD"},           // 2025-09-05
  "time1": {"format": "HH:mm:ss"},             // 14:30:45
  "time2": {"format": "hh:mm A"},              // 02:30 PM
  "datetime": {"format": "MM/DD/YYYY HH:mm"}   // 09/05/2025 14:30
}
```

---

## Number Formatting

### Decimal Places

```json
{
  "price": {"format": ".2f"}   // 25.99
}
```

### Percentage

```json
{
  "discount": {"format": "%"}  // 15.5%
}
```

---

## Tips and Best Practices

### 1. Paper Width

Standard thermal printers use 42 characters width. Adjust if your printer is different:

```javascript
const formatter = new ReceiptFormatter(48); // 48 characters width
```

### 2. Column Widths

Make sure table column widths add up to your paper width:

```json
{
  "columns": [
    {"text": "Item", "width": 22},    // 22
    {"text": "Qty", "width": 6},      // + 6
    {"text": "Price", "width": 14}    // + 14 = 42
  ]
}
```

### 3. Nested Sections

Use nested sections to organize your template:

```json
{
  "header": { /* header fields */ },
  "body": { /* body fields */ },
  "footer": { /* footer fields */ }
}
```

### 4. Data Mapping

Section names in template should match data structure:

```json
// Template
{
  "customer_info": {
    "name": {"prefix": "Name: "},
    "phone": {"prefix": "Phone: "}
  }
}

// Data
{
  "name": "John Doe",      // Will be found
  "phone": "123-456-7890"  // Will be found
}
```

### 5. Testing

Test your template before production:

```bash
node test-receipt-format.js
```

---

## Troubleshooting

### Fields Not Showing

**Problem**: Some fields don't appear in the receipt

**Solution**: Check that:
1. Field names in template match data keys
2. Data values are not `null` or `undefined`
3. Section names are correct

### Alignment Issues

**Problem**: Text not aligned properly

**Solution**: 
1. Check that column widths add up correctly
2. Ensure `width` property is set
3. Verify `align` property is spelled correctly

### Date Not Formatting

**Problem**: Date shows as ISO string instead of formatted

**Solution**:
1. Ensure `format` property is set
2. Check date format tokens are correct
3. Verify data contains valid date string or Date object

### Bold Not Working

**Problem**: Text not appearing bold

**Solution**:
1. Check printer supports ESC/POS bold command
2. Verify `bold: true` is set
3. Test with printer's test page

---

## Support

Need help with templates?

- **📧 Email**: support-printelyconnect@khatratech.com
- **📖 API Docs**: [API_DOCUMENTATION.md](API_DOCUMENTATION.md)
- **🐛 Issues**: GitHub Issues

---

**© 2025 KhatraTech. All rights reserved.**
