- System Information:
- ℹ️ 8192 MB GDDR5 VRAM, Intel i7-8700 CPU @3.2o GHz.

## Issue:
Docker Model Runner wasn't using my GPU for offloading computational layers.
![](/Pasted%20image%2020251102212807.png)
## Solution Strategy
Build Llama.cpp with Vulkan support.
Instructions to be followed from Llama.cpp [GitHub](https://github.com/ggml-org/llama.cpp/blob/master/docs/build.md#vulkan)

## Steps:
- ⭐ Install [Git-SCM](https://git-scm.com/install/windows)
- ⭐ Install [CMake](https://github.com/Kitware/CMake/releases/download/v4.2.0-rc2/cmake-4.2.0-rc2-windows-x86_64.msi)
- ⭐ Obviously, have VS Code, Python 3.9+.
- ⭐ Install HuggingFace CLI from PowerShell
	- ⭐ `powershell -ExecutionPolicy ByPass -c "irm https://hf.co/cli/install.ps1 | iex"`
- 🖖 Install Vulkan SDK, Runtime, Components and Config from [here](https://vulkan.lunarg.com/sdk/home#windows)
- ⭐ Install CURL for Windows from [here](https://curl.se/windows/) . 
	- ⭐ Extract to a folder, add the path of the `bin` subfolder to the System Environment Variable PATH
- ⭐ Restart the PC _(not mentioned anywhere, but good to have any SDK paths and system variables to take in to effect)_ 
- ⭐ Clone the [Llama.cpp repo](https://github.com/ggerganov/llama.cpp) to your PC
- ⭐ Open llama.cpp repo in explorer, right click and choose `Open Git Bash here`
- 🖖 Run these commands in Git Bash to build llama.cpp with Vulkan support:
```
cmake -B build -DGGML_VULKAN=ON -DCURL_INCLUDE_DIR='D:/Programs/cURL/curl-8.16.0_14-win64-mingw/include' -DCURL_LIBRARY='D:/Programs/cURL/curl-8.16.0_14-win64-mingw/lib/libcurl.dll.a'

cmake --build build --config Release
```

Build files created:
![](/Pasted%20image%2020251102124857.png)

After a fair bit of compiler warnings, you should be able to see successful compilation message as follows:
![](/Pasted%20image%2020251102125534.png)

### Verifying the build:
Execute `vulkaninfo | grep -i RX` to see my Radeon RX 580 listed:
![](/Pasted%20image%2020251102125411.png)

## Lets download some models
### Starting with some smaller ones:

- ⭐ Create an access token for your HuggingFace account from [here](https://huggingface.co/settings/tokens) Note down the token to somewhere secret and secure.
- ⭐ In a cmd terminal, login to HF using the token:
	- ⭐ `hf auth login --token <Your_Token>`
- ⭐ Create a folder to store LLM models, and execute this command to download a small model - Phi-3. Choose a model in gguf format
	- ⭐ `huggingface-cli download microsoft/Phi-3-mini-4k-instruct-gguf Phi-3-mini-4k-instruct-q4.gguf --local-dir . --local-dir-use-symlinks False`

### Lets go~
Play around with the `-ngl` and `-c` (context size) for individual cases. Same with other parameters like `-t`

`./build/bin/Release/llama-cli -m ""D:/Development/AIML/Models/Phi-3-mini-4k-instruct-q4.gguf"" -ngl 67 -c 8192 -t 6 -b 512 --temp 0.7 --top-k 40 --top-p 0.9 -p "What is an Event Horizon? Explain like I'm 5."`


## Results:
At Rest:
![Screenshot at rest](/Pasted%20image%2020251102124857.png)

During Inferencing
![](/Pasted%20image%2020251102134412.png)

Post Inference
![](/Pasted%20image%2020251102134431.png)

After Model offload
![](/Pasted%20image%2020251102134527.png)

