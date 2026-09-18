<div align="center">
  <img src="custom_components/grocery_deals/brand/logo.png" alt="Grocery Deals Banner" width="400">
  <h1>Grocery Deals (for Home Assistant) 🛒🏷️</h1>
  <p><strong>Universal Supermarket Deals & Discount Aggregator for Home Assistant. Automatically aggregates offers across REWE, EDEKA, Lidl, ALDI, Norma, and Kaufland to find the best prices and notify you when your favorite products are on sale!</strong></p>

  [![HACS Custom](https://img.shields.io/badge/HACS-Custom-orange.svg?style=for-the-badge)](https://hacs.xyz)
  [![Downloads](https://img.shields.io/github/downloads/FaserF/ha-grocery-deals/latest/grocery_deals.zip?label=Downloads&style=for-the-badge)](https://github.com/FaserF/ha-grocery-deals/releases)
  [![GitHub Release](https://img.shields.io/github/v/release/FaserF/ha-grocery-deals?style=for-the-badge)](https://github.com/FaserF/ha-grocery-deals/releases)
  [![License](https://img.shields.io/github/license/FaserF/ha-grocery-deals?style=for-the-badge)](LICENSE)
</div>

---

## 🧭 Quick Links

| | | | |
| :--- | :--- | :--- | :--- |
| [✨ Features](#-features) | [🛒 Supported Supermarkets](#-supported-supermarkets) | [📦 Installation](#-installation) | [⚙️ Configuration](#-configuration) |
| [🛠️ Options Flow](#-options-flow) | [❤️ Sponsors](#-support-this-project) | [📄 License](#-license) | |

---

## 🛒 Supported Supermarkets

> [!IMPORTANT]
> **Requirement**: You must have **at least two** of the following supermarket integrations installed and configured in Home Assistant so Grocery Deals can compare prices and find the cheapest store:

| Supermarket | Integration & Repository | Features Provided |
| :--- | :--- | :--- |
| 🔴 **REWE** | [**ha-rewe**](https://github.com/FaserF/ha-rewe) | Weekly offers, REWE Bonus coupons, recalls |
| 🔴 **PENNY** | [**ha-penny**](https://github.com/FaserF/ha-penny) | Digital receipts (eBons), PDF item breakdown & loyalty points |
| 🟡 **EDEKA** | [**ha-edeka**](https://github.com/FaserF/ha-edeka) | Regional market offers & discounts |
| 🔵 **Lidl** | [**ha-lidl**](https://github.com/FaserF/ha-lidl) | Weekly offers, Lidl Plus coupons, digital receipts |
| ⚪ **ALDI** | [**ha-aldi**](https://github.com/FaserF/ha-aldi) | ALDI Süd / ALDI Nord weekly flyers & deals |
| 🔴 **Norma** | [**ha-norma**](https://github.com/FaserF/ha-norma) | Weekly store discounts & promotional brochures |
| 🟢 **Kaufland** | [**ha-kaufland**](https://github.com/dev-ath0m/ha-kaufland) | Weekly offers & discounts |

---

## 🔧 Kaufland support

Kaufland support is intentionally implemented as a small adapter in Grocery Deals rather than duplicating Kaufland's offer-fetching logic. The `ha-kaufland` integration already provides its weekly offers through its coordinator; Grocery Deals now registers the `kaufland` domain, consumes its `discounts` data alongside the existing supermarket schemas, and recognizes Kaufland's `date_to` validity field. This keeps Kaufland data sourced from the dedicated `ha-kaufland` integration while allowing Grocery Deals to aggregate it with the other providers.

## ✨ Features

- **🔍 Custom Keyword Filtering**:
  - Configure any list of search terms (e.g. `Monster Energy`, `Butter`, `Kaffee`, `Gurke`).
  - For each keyword, a dedicated sensor is generated:
    - **State**: Current best price & store (e.g., `0,88 € (Lidl)`) or `Nicht im Angebot`.
    - **Attributes**:
      - `on_sale`: `True` / `False`
      - `best_price`: Lowest price found across all markets
      - `best_store`: Name of the market offering the best price
      - `on_sale_stores`: List of all markets where the item is currently on sale
      - `all_prices`: Complete price breakdown grouped by supermarket
      - `offers`: All matching offer details with images, validity dates, base prices, and pack sizes.
- **🚀 Dynamic Auto-Discovery**:
  - Automatically discovers when 2 or more supermarket integrations are set up on your Home Assistant.
  - Zero-touch prompt to enable the aggregator hub.
- **📊 Hub Overview Sensor**:
  - Shows the total count of active deals across all your keywords and lists which stores are connected.
- **⚡ Zero Additional API Load**:
  - Seamlessly re-uses the cached coordinator data from your installed supermarket integrations without spamming external supermarket APIs.

---

## 🃏 Lovelace card

Grocery Deals exposes the same canonical offer fields regardless of supermarket provider. That means a standard Home Assistant Markdown card can display Kaufland, REWE, EDEKA, Lidl, ALDI, NORMA and other supported offers without provider-specific templates.

Add a **Markdown** card to your dashboard:

~~~yaml
type: markdown
entity_id:
  - sensor.grocery_deals_active_deals
content: |
  ## 🛒 Grocery Deals

  {% set deals = state_attr('sensor.grocery_deals_active_deals', 'all_matched_deals') or [] %}
  {% if deals %}
  {% for deal in deals %}
  {% if deal.picture_link %}
  <img src="{{ deal.picture_link }}" width="110" align="left" style="margin-right:12px;">
  {% endif %}
  **{{ deal.product_title }}**  
  🏪 {{ deal.store_title }}  
  💶 **{{ deal.price }}**{% if deal.base_price %} · {{ deal.base_price }}{% endif %}  
  {% if deal.valid_until %}📅 gültig bis {{ deal.valid_until }}{% endif %}

  ---
  {% endfor %}
  {% else %}
  Keine passenden Angebote gefunden.
  {% endif %}
~~~

For a single product filter, use its filter sensor and the `offers` attribute:

~~~yaml
type: markdown
entity_id:
  - sensor.grocery_deals_filter_butter
content: |
  {% set offers = state_attr('sensor.grocery_deals_filter_butter', 'offers') or [] %}
  {% for offer in offers %}
  **{{ offer.product_title }}** — **{{ offer.price }}**  
  🏪 {{ offer.store_title }}{% if offer.base_price %} · {{ offer.base_price }}{% endif %}  
  {% if offer.valid_until %}📅 bis {{ offer.valid_until }}{% endif %}

  ---
  {% endfor %}
~~~

## ❤️ Support This Project

> I maintain this integration in my **free time alongside my regular job**.
>
> **This project is and will always remain 100% free.**
>
> Donations are completely voluntary — but they help me stay motivated and dedicate more time to maintaining open-source tools!

<div align="center">

[![PayPal](https://img.shields.io/badge/Donate%20via-PayPal-%2300457C?style=for-the-badge&logo=paypal&logoColor=white)](https://paypal.me/FaserF)

</div>

---

## 📦 Installation

### HACS (Recommended)

This fork requires **two parts**: Grocery Deals itself and the supermarket integrations that provide the offer data. You need at least **two supported supermarket integrations** configured in Home Assistant.

#### 1. Install Grocery Deals

1. Open **HACS** in Home Assistant.
2. Go to **Integrations** → **⋮** → **Custom repositories**.
3. Add `dev-ath0m/ha-grocery-deals-kaufland-fix` and select **Integration**.
4. Find **Grocery Deals** and install it.
5. Restart Home Assistant.

#### 2. Install the supermarket integrations

Install and configure at least two supported supermarket integrations. For Kaufland, use the dedicated **ha-kaufland** integration:

- `dev-ath0m/ha-kaufland` — provides Kaufland's weekly offers and discount data.
- Other supported integrations can be installed from their respective repositories listed above.

For a HACS installation, add `dev-ath0m/ha-kaufland` under **HACS → Integrations → ⋮ → Custom repositories**, select **Integration**, then install **ha-kaufland** and restart Home Assistant.

#### 3. Configure Grocery Deals

1. Go to **Settings → Devices & services → Add integration**.
2. Select **Grocery Deals**.
3. Enter the product filters you want to track.
4. Grocery Deals automatically detects the configured supermarket integrations.
5. Make sure at least two supported supermarket integrations are configured before enabling the aggregator.

### Manual Installation

1. Download or clone this repository.
2. Copy `custom_components/grocery_deals/` into your Home Assistant `<config>/custom_components/` directory.
3. Install and configure at least two supported supermarket integrations separately.
4. Restart Home Assistant.
5. Go to **Settings → Devices & services → Add integration** and add **Grocery Deals**.

---

## ⚙️ Configuration & Options

1. Go to **Settings > Devices & Services > Add Integration**.
2. Search for **Grocery Deals**.
3. The setup screen will show you which supermarket integrations were detected and which ones you can optionally install from GitHub.
4. Enter your desired search filter keywords.
5. Done! You can modify or add new filter keywords anytime via the **Configure** (Options) button.

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).
