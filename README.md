# MongoDB-AWS-Fraud-Detection

# Download the 2 CSV Data Files

## Data Ingestion Using mongoimport in the Terminal (slower alternative to Compass)
```
mongoimport --uri="<connection-string>" --db=fraud_detection --collection=Identity --file=Fraud_identity.csv --type=csv --headerline
```
```
mongoimport --uri="<connection-string>" --db=fraud_detection --collection=Transaction --file=Fraud_transaction.csv --type=csv --headerline
```

# S3 Path
```
s3://<your-bucket-name>/fraud-detection/canvas/
```

# Identity App Services Function
```
exports = function () {
  const service = context.services.get("mongodb-datafederation");
  const db = service.db("fraud_detection")
  const events = db.collection("Identity");
  const pipeline = [
    {
      "$out": {
        "s3": {
          "bucket": "your-S3-bucket-name",
          "region": "us-east-1",
          "filename": "fraud-detection/canvas/Identity",
          "format": {
            "name": "csv",
            "maxFileSize": "10GB"
          }
        }
      }
    }];
  return events.aggregate(pipeline);
};
```
# Transaction App Services Function
```
exports = function () {
  const service = context.services.get("mongodb-datafederation");
  const db = service.db("fraud_detection")
  const events = db.collection("Transaction");
  const pipeline = [
    {
      "$out": {
        "s3": {
          "bucket": "your-S3-bucket-name",
          "region": "us-east-1",
          "filename": "fraud-detection/canvas/Transaction",
          "format": {
            "name": "csv",
            "maxFileSize": "10GB"
          }
        }
      }
    }];
  return events.aggregate(pipeline);
};
```
