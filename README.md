# LKS Course - Frontend Application

Modern course catalog and order management system built with Nuxt.js, featuring a proxy architecture for secure backend communication.

## Architecture Overview

This frontend acts as a **proxy server** for all backend requests, routing traffic through the Nuxt.js application to the AWS ELB backend. This approach provides better security, centralized request handling, and CORS management.

```
Client → Frontend Proxy (localhost:3000/api/*) → AWS ELB Backend
```

## Environment Configuration

### Required Environment Variables

Create a `.env` file in the project root:

```bash
# Backend API URL - AWS ELB endpoint
NUXT_ENV_API_URL=http://internal-lks-lb-backend-271785892.us-east-1.elb.amazonaws.com
```

**Note**: If `NUXT_ENV_API_URL` is not set, the application will fallback to the hardcoded AWS ELB URL in `nuxt.config.js`.

### How Proxy Works

All API requests are now routed through the frontend:

- **Old approach**: Client → Direct to backend URL
- **New approach**: Client → Frontend proxy → Backend URL

Example:
```javascript
// All these requests go through frontend proxy at /api/*
await this.$axios.get('/api/v1/course')           // GET courses
await this.$axios.post('/api/v1/order', data)     // Create order  
await this.$axios.delete('/api/v1/order/123')     // Delete order
```

The proxy configuration in `nuxt.config.js` handles routing to the backend automatically.

## Prerequisites

### Node.js Installation

For Amazon Linux:
```bash
sudo yum install -y gcc-c++ make
curl -sL https://rpm.nodesource.com/setup_18.x | sudo -E bash -
sudo yum install -y nodejs
```

For Ubuntu/Debian:
```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs
```

## Installation

```bash
# Install dependencies
npm install
```

## Running the Application

### Development Mode

For local development with hot-reload:

```bash
npm run dev
```

The application will be available at `http://localhost:3000`

### Production Deployment

#### Option 1: Server-Side Rendering (SSR)

Best for dynamic content and SEO optimization:

```bash
# Build the application
npm run build

# Start in production mode
npm run start
```

The server will listen on `0.0.0.0:3000` (configurable in `nuxt.config.js`).

#### Option 2: Static Generation (SSG)

For static hosting (Nginx, Apache, S3, etc.):

```bash
# Generate static files
npm run generate

# Output will be in the 'dist' folder
# Deploy the contents of 'dist' to your static web server
```

## Project Structure

```
lks-course-frontend/
├── assets/          # Uncompiled assets (SCSS, images)
├── components/      # Reusable Vue components
├── layouts/         # Application layouts
├── middleware/      # Custom middleware
├── pages/           # Application views and routes
│   ├── index.vue       # Course catalog page
│   ├── AddCourse.vue   # Add new course form
│   └── Order.vue       # Order management page
├── plugins/         # JavaScript plugins
├── static/          # Static files (directly served)
├── store/           # Vuex store modules
├── nuxt.config.js   # Nuxt configuration (includes proxy setup)
└── package.json     # Project dependencies
```

## Key Features

- **Course Catalog Management**: Browse and manage course listings
- **Shopping Cart**: Add courses to cart and purchase
- **Order Management**: View and manage course orders
- **Proxy Architecture**: Secure backend communication through frontend
- **Responsive Design**: Mobile-friendly UI with Vuetify and Tailwind CSS
- **File Upload Support**: Course cover image uploads with multipart/form-data

## API Endpoints (Proxied)

All requests to `/api/*` are proxied to the backend:

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/v1/course` | List all courses |
| POST | `/api/v1/course` | Create new course |
| DELETE | `/api/v1/course/:id` | Delete course |
| GET | `/api/v1/order` | List all orders |
| POST | `/api/v1/order` | Create new order |
| DELETE | `/api/v1/order/:id` | Delete order |

## Configuration

### Proxy Settings

The proxy is configured in `nuxt.config.js`:

```javascript
proxy: {
  '/api/': {
    target: process.env.NUXT_ENV_API_URL || 'http://internal-lks-lb-backend-271785892.us-east-1.elb.amazonaws.com',
    changeOrigin: true,
  }
}
```

**Supported HTTP Methods**: GET, POST, PUT, DELETE, PATCH, OPTIONS, HEAD

### Port Configuration

Default port is `3000`. To change, set in `package.json`:

```json
"scripts": {
  "start": "nuxt start --hostname 0.0.0.0 --port 8080"
}
```

## Testing

Run unit tests:

```bash
npm test
```

## Troubleshooting

### Proxy Issues

If requests are not proxying correctly:

1. Check `NUXT_ENV_API_URL` is set correctly
2. Verify backend is accessible from the frontend server
3. Check browser console Network tab - requests should show `/api/*` not full URLs
4. Review Nuxt dev server logs for proxy errors

### CORS Errors

The proxy should handle CORS automatically via `changeOrigin: true`. If you still see CORS errors:

1. Ensure you're using relative paths (`/api/v1/...`) not full URLs
2. Check backend CORS configuration
3. Verify proxy is enabled in `nuxt.config.js`

### Build Errors

Clear cache and reinstall:

```bash
rm -rf node_modules .nuxt dist
npm install
npm run build
```

## Development Notes

- Old API calls are commented out in the code for reference
- Each API call has explanatory comments showing the proxy setup
- To rollback proxy changes, uncomment old code and remove new proxy calls
- Backend URL is configurable via environment variable

## Support

For issues or questions:
- Check commented code for implementation references
- Review `nuxt.config.js` proxy configuration
- Verify environment variables are set correctly

## License

Private - For LKS Course Project
