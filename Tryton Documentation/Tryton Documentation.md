# Installation

```ad-summary
https://hub.docker.com/r/tryton/tryton

Alternatively, you can deploy and download with a compose file. This is what we should do incase we need to add more dependencies associated with tryton. 
```

An `.env` file must be created in the same directory as the `.yml` compose file to create the variables. 

> [!example] `DB_PASSWORD`, `PASSWORD`, `DB_NAME`

> [!danger] We need to ***ensure*** that the `.env` file has the proper permissions so passwords cannot be accessed in cleartext
> 

Tryton ERP (open-source enterprise resource planning suite)

Why It Fits Atlas Industrial LLC

Tryton is a modular, open-source ERP designed for managing inventory, production, supply chains, and accounting workflows. This aligns directly with Atlas Industrial’s mission to manufacture and distribute computer parts. Because Tryton includes modules for:

- Inventory and warehouse management

- Procurement and order tracking

- Production workflows

- Sales and customer management

- Supply-chain tracking

- Integrated PostgreSQL support

…it enables Atlas Industrial LLC to track goods from production to delivery, supporting the organization’s critical capabilities and critical requirements defined in Stage 1.

In other words, Tryton enhances the company’s ability to produce and deliver goods on time — a core mission element.