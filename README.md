# 🛒 WhatsApp E-Commerce SaaS (Libromi Clone)
<img width="1635" height="673" alt="image" src="https://github.com/user-attachments/assets/9331892d-f62c-4e43-9b26-547f2c4fd9ed" />

> A fully automated WhatsApp store built with **n8n**, **Google Sheets**, and **Gemini AI**.

This project converts a simple WhatsApp number into a full-fledged E-commerce store. It features an interactive product menu, a shopping cart, an AI sales assistant, and a complete checkout system—all powered by a low-code backend.

![Project Status](https://img.shields.io/badge/Status-Active-success)
![Stack](https://img.shields.io/badge/Stack-n8n_|_WhatsApp_API_|_Google_Sheets-blue)

## 🏗️ Architecture

The system follows a **"Fresh v2"** architecture where n8n acts as the central brain, orchestrating traffic between the user (WhatsApp) and the database (Google Sheets).

**Flow Overview:**
1.  **Ingestion:** Webhook receives messages from WhatsApp Cloud API.
2.  **Normalization:** Converts complex Meta JSON into a clean object.
3.  **State Machine:** Checks if the user is browsing, buying, or typing an address.
4.  **Router:** Directs traffic to:
    * **Storefront:** Generates dynamic interactive menus.
    * **Cart Logic:** Adds items and calculates totals.
    * **Checkout:** Collects address, generates Order ID, and confirms the order.
    * **AI Agent:** Handles general queries using Google Gemini 1.5 Flash.

---

## ✨ Features

* **📱 Interactive Menu:** Dynamic product lists with a "Checkout" button that updates the total price in real-time (e.g., `🛒 Checkout (₹1200)`).
* **🛒 Persistent Cart:** Users can add multiple items; data is stored in Google Sheets.
* **🤖 AI Sales Agent:** Google Gemini AI handles non-transactional queries (e.g., "Do you sell sci-fi books?").
* **📍 Address & State Management:** Smart "State Machine" logic detects when a user is providing shipping details.
* **📦 Order Management:** Generates unique Order IDs (`ORD-XXXXX`), saves order history, and clears the cart post-purchase.
* **💸 Cash on Delivery (COD) Flow:** Automated order confirmation messages with a summary of items and shipping address.

---

## 🛠️ Tech Stack

* **Frontend:** WhatsApp Interface (Interactive Lists & Buttons)
* **Backend Logic:** [n8n](https://n8n.io/) (Self-hosted or Cloud)
* **Database:** Google Sheets
* **AI Model:** Google Gemini 1.5 Flash
* **Tunneling:** ngrok (for local development)

---

## 📋 Database Schema (Google Sheets)

Create a Google Sheet with the following **4 Tabs**. The column names must match exactly.

### 1. `Products`
| id | title | price | description |
| :--- | :--- | :--- | :--- |
| 101 | Atomic Habits | 500 | Build good habits |
| 102 | The Alchemist | 300 | A fable about following your dream |

### 2. `Users`
| wa_id | name | step | address |
| :--- | :--- | :--- | :--- |
| 919876543210 | John Doe | idle | 123 Main St, NY |

### 3. `Carts`
| wa_id | product_id | quantity | timestamp |
| :--- | :--- | :--- | :--- |
| 919876543210 | 101 | 1 | 2023-10-25... |

### 4. `Orders`
| order_id | wa_id | items | total_amount | status | address |
| :--- | :--- | :--- | :--- |
| ORD-48291 | 919876543210 | Atomic Habits(x1) | 500 | CONFIRMED | 123 Main St |

---

## 🚀 Installation & Setup

### 1. n8n Setup
1.  Install n8n locally (`npm install n8n -g`) or use the Cloud version.
2.  Import the `workflow.json` file included in this repo.
3.  Configure your credentials in n8n for:
    * **Google Sheets API** (OAuth2 or Service Account)
    * **WhatsApp Cloud API** (Access Token)
    * **Google Gemini API**

### 2. WhatsApp Configuration (Meta Developers)
1.  Create an app in the [Meta Developer Portal](https://developers.facebook.com/).
2.  Add **WhatsApp** product.
3.  Get your **Phone Number ID** and **Permanent Access Token**.
4.  Configure the **Webhook**:
    * **Callback URL:** Your n8n webhook URL (use `ngrok` for local dev).
    * **Verify Token:** Any string (you must handle the `GET` challenge in n8n first).

### 3. Google Sheets Connection
1.  Connect the `Google Sheets` nodes in n8n to the sheet created in the step above.
2.  Ensure the "Read/Write" permissions are active.

---

## 🔌 Local Development (ngrok)

To run this project locally, you need to expose your localhost to the internet.

1.  Start n8n:
    ```bash
    n8n start
    ```
2.  Start ngrok tunnel:
    ```bash
    ngrok http 5678
    ```
3.  Copy the `https` URL from ngrok (e.g., `https://1a2b-3c4d.ngrok-free.app`).
4.  Update your Meta Developer Webhook URL to: `https://1a2b-3c4d.ngrok-free.app/webhook`

---

## 📸 Workflow Logic

The n8n workflow consists of 7 key phases:
1.  **Normalization:** Flattens incoming JSON.
2.  **Gatekeeper:** Checks user state (Idle vs. Waiting for Address).
3.  **Router:** Splits traffic based on intent (Menu, Add to Cart, Checkout, AI).
4.  **Menu Generation:** Javascript node that calculates current cart total and renders the list.
5.  **Cart Management:** Appends rows to the `Carts` sheet.
6.  **Checkout Engine:** Merges `Cart` + `Products`, calculates total, saves to `Orders`, and clears `Carts`.
7.  **Response:** Sends the final WhatsApp message.

---

## 🔮 Future Roadmap

- [ ] Integration with Razorpay Payment Gateway (API).
- [ ] Admin Dashboard for Order Management (Retool/Appsmith).
- [ ] Automated PDF Invoice generation.
- [ ] Multi-language support.

---

## 📄 License

This project is open-source and available under the [MIT License](LICENSE).
