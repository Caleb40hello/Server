// Import the Express framework and the built-in Node.js crypto and cors modules.
const express = require('express');
const crypto = require('crypto');
const cors = require('cors'); 

// Initialize the Express app.
const app = express();
const PORT = process.env.PORT || 3000; // Use the port provided by the hosting environment or default to 3000.

// Middleware to parse JSON bodies from incoming requests.
app.use(express.json());
app.use(cors()); // Enable CORS for all routes.

// A simple in-memory "database" to store generated codes.
// In a real application, you would use a proper database like MongoDB or PostgreSQL.
const generatedCodes = new Map();

/**
 * Generates a cryptographically secure, random 15-character code.
 * The code includes letters (uppercase and lowercase), numbers, and special characters.
 * @returns {string} The generated code.
 */
function generateCfcCode() {
    const characters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789!@#$%^&*()-_=+[]{}|;:,.<>?';
    const length = 15;
    
    // Use crypto.randomBytes for true randomness.
    const randomBytes = crypto.randomBytes(length);
    let code = '';
    
    for (let i = 0; i < length; i++) {
        // Use the modulus operator to map the random byte to an index in our character set.
        code += characters[randomBytes[i] % characters.length];
    }
    
    return code;
}

/**
 * Verifies if a submitted code is valid.
 * This function checks if the code exists in our temporary storage.
 * @param {string} submittedCode The code the user provided.
 * @returns {boolean} True if the code is valid, false otherwise.
 */
function verifyCfcCode(submittedCode) {
    // Check if the code exists in our map.
    return generatedCodes.has(submittedCode);
}

// --- API Endpoints ---

// Endpoint to generate a new CFC code.
app.get('/generate-code', (req, res) => {
    const newCode = generateCfcCode();
    
    // Store the generated code with a timestamp to simulate a limited lifespan.
    const codeData = {
        code: newCode,
        createdAt: Date.now()
    };
    
    generatedCodes.set(newCode, codeData);
    
    console.log(`Generated new code: ${newCode}`);
    res.status(200).json({
        message: 'A new CFC code has been generated and is ready for use.',
        code: newCode
    });
});

// Endpoint to verify a submitted code.
app.post('/verify-code', (req, res) => {
    const { code } = req.body;
    
    if (!code) {
        return res.status(400).json({ error: 'Code is required.' });
    }
    
    // Check for a valid code in our storage.
    const isValid = verifyCfcCode(code);
    
    if (isValid) {
        // To prevent reuse, delete the code after successful verification.
        generatedCodes.delete(code);
        console.log(`Code "${code}" was successfully verified and removed.`);
        res.status(200).json({ message: 'CFC code verified successfully!' });
    } else {
        res.status(401).json({ error: 'Invalid or expired CFC code.' });
    }
});

// A basic root route to check if the server is running.
app.get('/', (req, res) => {
    res.status(200).send("Cash Flow Server is running!");
});


// Start the server.
app.listen(PORT, () => {
    console.log(`Server is running on port ${PORT}`);
});
