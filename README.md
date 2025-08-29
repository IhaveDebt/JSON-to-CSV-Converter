// json-to-csv.ts
import * as fs from "fs";
import * as path from "path";

function jsonToCsv(jsonArray: any[]): string {
  if (jsonArray.length === 0) return "";

  const headers = Object.keys(jsonArray[0]);
  const csvRows = [headers.join(",")];

  jsonArray.forEach(obj => {
    const values = headers.map(header => {
      let val = obj[header];
      if (typeof val === "string") {
        val = `"${val.replace(/"/g, '""')}"`; // escape quotes
      }
      return val;
    });
    csvRows.push(values.join(","));
  });

  return csvRows.join("\n");
}

function convertJsonFileToCsv(inputPath: string, outputPath: string) {
  if (!fs.existsSync(inputPath)) {
    console.error(`❌ File not found: ${inputPath}`);
    process.exit(1);
  }

  const rawData = fs.readFileSync(inputPath, "utf-8");
  let jsonData;

  try {
    jsonData = JSON.parse(rawData);
  } catch (err) {
    console.error("❌ Invalid JSON file.");
    process.exit(1);
  }

  if (!Array.isArray(jsonData)) {
    console.error("❌ JSON must be an array of objects.");
    process.exit(1);
  }

  const csvData = jsonToCsv(jsonData);
  fs.writeFileSync(outputPath, csvData);
  console.log(`✅ Converted JSON to CSV: ${outputPath}`);
}

// Example usage
const inputFile = process.argv[2];
const outputFile = process.argv[3] || "output.csv";

if (!inputFile) {
  console.log("Usage: ts-node json-to-csv.ts <input.json> [output.csv]");
  process.exit(0);
}

const inputPath = path.resolve(inputFile);
const outputPath = path.resolve(outputFile);

convertJsonFileToCsv(inputPath, outputPath);
