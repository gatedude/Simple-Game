const { ethers } = require("ethers");

// Replace these values with actual ones
const DEGEN_ADDRESS = "0xYourDegenTokenAddress"; // DEGEN token contract address
const WETH_ADDRESS = "0xYourWethTokenAddress";   // WETH token contract address
const DEX_ROUTER_ADDRESS = "0xYourDexRouterAddress"; // DEX router contract address
const PRIVATE_KEY = "your_private_key"; // Your wallet private key
const RPC_URL = "https://degenchain.rpc.url"; // RPC URL for Degen Chain

async function convertDEGENtoWETH(amount) {
    // Connect to the provider
    const provider = new ethers.providers.JsonRpcProvider(RPC_URL);
    const wallet = new ethers.Wallet(PRIVATE_KEY, provider);
    
    // Create contract instances
    const degenContract = new ethers.Contract(DEGEN_ADDRESS, [
        "function approve(address spender, uint256 amount) public returns (bool)"
    ], wallet);
    
    const routerContract = new ethers.Contract(DEX_ROUTER_ADDRESS, [
        "function swapExactTokensForTokens(uint256 amountIn, uint256 amountOutMin, address[] calldata path, address to, uint256 deadline) external returns (uint256[] memory amounts)"
    ], wallet);

    // Approve DEGEN for spending
    const approveTx = await degenContract.approve(DEX_ROUTER_ADDRESS, amount);
    await approveTx.wait();

    // Define swap parameters
    const amountOutMin = 0; // Set minimum amount you expect to receive
    const path = [DEGEN_ADDRESS, WETH_ADDRESS]; // Path from DEGEN to WETH
    const to = wallet.address; // Your wallet address
    const deadline = Math.floor(Date.now() / 1000) + 60 * 20; // 20 minutes from now

    // Execute the swap
    const swapTx = await routerContract.swapExactTokensForTokens(
        amount,
        amountOutMin,
        path,
        to,
        deadline
    );
    await swapTx.wait();

    console.log(`Successfully swapped ${amount} DEGEN to WETH.`);
}

// Call the function with the amount of DEGEN you want to convert
convertDEGENtoWETH(ethers.utils.parseUnits("10.0", 18)); // Example for 10 DEGEN