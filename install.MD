How to run Meta’s LLaMA on your computer [Windows, Linux tutorial]
https://aituts.com/llama/
[magnet:?xt=urn:btih:ZXXDAUWYLRUXXBHUYEMS6Q5CE5WA3LVA&dn=LLaMA](magnet:?xt=urn:btih:ZXXDAUWYLRUXXBHUYEMS6Q5CE5WA3LVA&dn=LLaMA)

On March 3rd, user ‘llamanon’ leaked Meta’s LLaMA model on 4chan’s technology board /g/, enabling anybody to torrent it. A troll attempted to add the torrent link to Meta’s official LLaMA Github repo.


Even though LLaMA is technically an ‘open-source’ model that was slowing being given to researchers who applied on the Meta website, it’s important to note that LLaMA is not fully ‘open’.

You have to agree to strict terms to access the model, and you aren’t allowed to use it for commercial purposes.

Llamanon has put LLaMA into the hands of thousands of people around the world, who are just starting to realize what happens when you can run a GPT-3 class model on your own computer.

LLaMA factsheet:

There are four different pre-trained LLaMA models, with 7B (billion), 13B, 30B, and 65B parameters.
Meta reports that the LLaMA-13B model outperforms GPT-3 in most benchmarks.
It also reports the 65B model is on-parr with the best models in the world, such as Google’s PaLM-540B.
4-bit LLaMA
The few hours after the model was released, developers around the world were furiously trying to get it running on their own computers.

We can now run LLaMA on most consumer grade computers with 4-bit quantization, a technique for reducing the size of models so they can run on less powerful hardware. It also reduces the model sizes on disk.

It works great!

We’re running the 7B & 13B LLaMA model on our laptops (the 13B model is the one that Facebook claims is competitive with GPT-3).

Here’s some user-reported requirements for each model (Windows/Linux). For Mac M1/M2, please look at these instructions instead.

Model	Model Size	Minimum Total VRAM	Card examples	RAM/Swap to Load*
LLaMA-7B	3.5GB	6GB	RTX 1660, 2060, AMD 5700xt, RTX 3050, 3060	16 GB
LLaMA-13B	6.5GB	10GB	AMD 6900xt, RTX 2060 12GB, 3060 12GB, 3080, A2000	32 GB
LLaMA-30B	15.8GB	20GB	RTX 3080 20GB, A4500, A5000, 3090, 4090, 6000, Tesla V100	64 GB
LLaMA-65B	31.2GB	40GB	A100 40GB, 2×3090, 2×4090, A40, RTX A6000, 8000, Titan Ada	128 GB
Check your specs before attempting to run LLaMA.

1. Download Model Weights
Acquire the updated HFv2 weights by using the following torrent, or by converting yourself from original FB weights:

Magnet Link

If you don’t have a torrent client, we recommend qBitorrent.

We recommend starting by just downloading the 7B model, to run through the entire setup and get it working first.

2. Download Dependencies
Microsoft Visual C++
Download “2019 Visual Studio and other products” (requires creating a Microsoft account). It must be the 2019 version.

In the Visual Studio Build Tools installer, check the Desktop development with C++ option:


Miniconda
Now, install miniconda first if you don’t have it already. The default settings are OK.

3. Install Oobabooga
We recommend Oobabooga as the UI to run your models with. It’s a bit like AUTOMATIC1111’s Stable Diffusion WebUI, except for language instead of images.

Open a command prompt/terminal and navigate to the directory you want to put the Oobabooga folder in. Copy and paste these commands one at a time:

(Type y to proceed when prompted, and allow a few minutes for the packages to download)

(If you are getting conda issues, you will need to add conda to your PATH)

conda create -n textgen
conda activate textgen
conda install torchvision torchaudio pytorch-cuda=11.7 git -c pytorch -c nvidia
git clone https://github.com/oobabooga/text-generation-webui
cd text-generation-webui
pip install -r requirements.txt
Copy
The third line assumes that you have an NVIDIA GPU.

If you have an AMD GPU, replace the third command with this one:

pip3 install torch torchvision torchaudio --extra-index-url https://download.pytorch.org/whl/rocm5.2
Copy
If you have decided to run in CPU mode, replace the third command with this one:

conda install pytorch torchvision torchaudio git -c pytorch
Copy
Now install GPTQ-for-LLaMa by copying and pasting these commands one by one:

mkdir repositories
cd repositories
git clone https://github.com/qwopqwop200/GPTQ-for-LLaMa
cd GPTQ-for-LLaMa
python setup_cuda.py install
Copy
(as mentioned, you must download Microsoft Visual C++ or you will run into issues on the last step)

4. Download Config File
Navigate back to the root directory, and download the tokenizer/config files for the model size of your choice from decapoda-research:

cd ../..
python download-model.py --text-only decapoda-research/llama-7b-hf
Copy
(except substitute llama-7b for whichever model you actually downloaded in the first step)

5. Download 4-Bit Weights
Now, you will need the corresponding 4-bit model to the model you are downloading in step one.

LLaMA-7B int4: https://huggingface.co/decapoda-research/llama-7b-hf-int4/
LLaMA-13B int4: https://huggingface.co/decapoda-research/llama-13b-hf-int4/
LLaMA-30B int4: https://huggingface.co/decapoda-research/llama-30b-hf-int4/
LLaMA-65B int4: https://huggingface.co/decapoda-research/llama-65b-hf-int4/

Download and place directly into your models folder. For instance, models/llama-7b-4bit.pt.

6. Windows-only Configuration Step
If you are on Windows, you will have to do this or you will run into a ‘CUDA not found’ error:

Download the 2 .dll files here.

Move those 2 files into miniconda3\envs\textgen\lib\site-packages\bitsandbytes

(or if you’re using anaconda, anaconda3\env\textgen\Lib\site-packages\bitsandbytes)

Then open the folder: miniconda3\envs\textgen\lib\site-packages\bitsandbytes\cuda_setup 

 (or if you’re using anaconda, anaconda3\env\textgen\Lib\site-packages\bitsandbytes\cuda_setup)

Right click > edit the main.py with your favorite text editor. Make these changes:

Change ct.cdll.LoadLibrary(binary_path) to ct.cdll.LoadLibrary(str(binary_path)) two times in the file.

Then replace the line:
if not torch.cuda.is_available(): return 'libsbitsandbytes_cpu.so', None, None, None, None
with:
if torch.cuda.is_available(): return 'libbitsandbytes_cuda116.dll', None, None, None, None

Now you will be able to run models using 8 bit precision on Windows.

Start!
Wait until the model you downloaded in the first step finishes. Place in the folder text-generation-webui/models.

You should have 3 files in this models folder now:

The original model weights, you torrented in Step 1
(folder called llama-7b or equivalent for other model)
The corresponding tokenizer/config folder
(eg. folder called llama-7b-hf or equivalent for other model)
The corresponding 4-bit weights
(file called llama-7b-4bit.pt or equivalent for other model)
Now you can start the webUI. In command prompt/terminal:

python server.py --load-in-4bit --model llama-7b-hf
Copy
But remember to change llama-7b-hf to whatever model you are actually using. It might take a while to load the model.


Wait for the success message.

Open the WebUI by navigating to:

http://localhost:7860/
Copy

Troubleshooting thread: https://github.com/oobabooga/text-generation-webui/issues/147

Troubleshooting on Windows: https://github.com/oobabooga/text-generation-webui/issues/20

Make sure you scroll down and change the model to the LLaMA model:


A big thank you to the tireless devs and hackers who have made this possible.

Keep in mind this is all bleeding edge tech. Some things will break and other things will become obselete very quickly.
