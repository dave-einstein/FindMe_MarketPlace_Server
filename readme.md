# Cron-Walker

A Node.js cron service that periodically analyzes product and user-uploaded images using TensorFlow.js and MobileNet to generate automated category recommendations and tags.

## Features

- Automated image analysis using TensorFlow.js and MobileNet model
- Periodic execution using node-cron
- Updates product categories based on image content
- Processes user-uploaded images to generate recommendation tags
- Built with TypeScript for type safety
- Supabase integration for database operations

## System Requirements

- Node.js (v16.x or higher recommended)
- npm or yarn
- Supabase account and project

## Installation

1. Clone this repository

```
git clone <repository-url>
cd cron-walker
```

2. Install dependencies

```
npm install
```

3. Create a `.env` file in the root directory with the following variables:

```
SUPABASE_URL=your_supabase_url
SUPABASE_KEY=your_supabase_anon_key
CRON_SCHEDULE="*/30 * * * *"  # Runs every 30 minutes, adjust as needed
```

## Project Structure

```
cron-walker/
├── src/
│   ├── config/
│   │   └── db.ts            # Supabase configuration
│   ├── services/
│   │   ├── imageClassification.ts  # TensorFlow image classification
│   │   ├── imageService.ts         # Handles user image processing
│   │   └── productService.ts       # Handles product image processing
│   ├── types/
│   │   └── index.ts         # Type definitions
│   └── index.ts             # Main application entry point
├── .env                     # Environment variables
├── package.json
├── tsconfig.json
└── README.md
```

## Usage

### Development Mode

Run the service in development mode with hot-reloading:

```
npm run dev
```

### Production Build

Build and run the production version:

```
npm run build
npm start
```

### Linting

Check for code quality issues:

```
npm run lint
```

## How It Works

### Image Classification Service

The image classification service uses TensorFlow.js with a pre-trained MobileNet model to analyze images and extract meaningful category labels.

1. The model is initialized when the service starts
2. Images are fetched from their URLs and processed
3. The model predicts the top 3 categories for each image
4. Results are cleaned and returned as string arrays

### Product Processing

The product service:

1. Retrieves products with empty category arrays from the database
2. Analyzes all images associated with each product
3. Combines and deduplicates the prediction results
4. Updates the product's `maincategory` field with the top 3 categories

### User Image Processing

The image service:

1. Retrieves images with empty recommendation arrays
2. Processes each image through the classifier
3. Updates the image's `recommendations` field with the prediction results

## Customization

### Cron Schedule

Adjust the cron schedule in the `.env` file to control how frequently the service runs. The default is every 30 minutes.

Example patterns:

- `"*/10 * * * *"` - Every 10 minutes
- `"0 * * * *"` - Every hour at minute 0
- `"0 0 * * *"` - Once a day at midnight

### Model Configuration

The MobileNet model can be configured in `imageClassification.ts`:

```typescript
// Change model version or alpha for performance/accuracy tradeoffs
this.model = await mobilebnet.load({
  version: 2, // Try 1 for faster but less accurate predictions
  alpha: 1.0, // Try 0.75 or 0.5 for faster but less accurate predictions
});
```

## Troubleshooting

Common issues:

- **Error: Model not initialized** : Ensure TensorFlow.js can load properly (check network connectivity)
- **Memory issues** : If processing large batches of images, consider implementing pagination in the services
- **Canvas errors** : The canvas package sometimes has installation issues on certain platforms. See [node-canvas installation guide](https://github.com/Automattic/node-canvas#installation) for platform-specific requirements

## License

ISC License
