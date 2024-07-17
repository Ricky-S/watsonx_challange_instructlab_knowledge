[Skip to main content](https://openai.com/index/video-generation-models-as-world-simulators/#main)

### ResearchProductsSafetyCompany

February 15, 2024
**Video generation models as world simulators**[View Sora overview](https://openai.com/index/sora/)
We explore large-scale training of generative models on video data. Specifically, we train text-conditional diffusion models jointly on videos and images of variable durations, resolutions and aspect ratios. We leverage a transformer architecture that operates on spacetime patches of video and image latent codes. Our largest model, Sora, is capable of generating a minute of high fidelity video. Our results suggest that scaling video generation models is a promising path towards building general purpose simulators of the physical world.
This technical report focuses on (1) our method for turning visual data of all types into a unified representation that enables large-scale training of generative models, and (2) qualitative evaluation of Sora’s capabilities and limitations. Model and implementation details are not included in this report.
Much prior work has studied generative modeling of video data using a variety of methods, including recurrent networks,[1](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-1), [2](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-2), [3](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-3) generative adversarial networks,[4](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-4), [5](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-5), [6](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-6), [7](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-7) autoregressive transformers,[8](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-8), [9](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-9) and diffusion models.[10](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-10), [11](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-11), [12](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-12) These works often focus on a narrow category of visual data, on shorter videos, or on videos of a fixed size. Sora is a generalist model of visual data—it can generate videos and images spanning diverse durations, aspect ratios and resolutions, up to a full minute of high definition video.
**Turning visual data into patches**
We take inspiration from large language models which acquire generalist capabilities by training on internet-scale data.[13](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-13), [14](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-14) The success of the LLM paradigm is enabled in part by the use of tokens that elegantly unify diverse modalities of text—code, math and various natural languages. In this work, we consider how generative models of visual data can inherit such benefits. Whereas LLMs have text tokens, Sora has visual *patches*. Patches have previously been shown to be an effective representation for models of visual data.[15](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-15), [16](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-16), [17](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-17), [18](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-18) We find that patches are a highly-scalable and effective representation for training generative models on diverse types of videos and images.
At a high level, we turn videos into patches by first compressing videos into a lower-dimensional latent space,[19](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-19) and subsequently decomposing the representation into spacetime patches.
**Video compression network**
We train a network that reduces the dimensionality of visual data.[20](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-20) This network takes raw video as input and outputs a latent representation that is compressed both temporally and spatially. Sora is trained on and subsequently generates videos within this compressed latent space. We also train a corresponding decoder model that maps generated latents back to pixel space.
**Spacetime latent patches**
Given a compressed input video, we extract a sequence of spacetime patches which act as transformer tokens. This scheme works for images too since images are just videos with a single frame. Our patch-based representation enables Sora to train on videos and images of variable resolutions, durations and aspect ratios. At inference time, we can control the size of generated videos by arranging randomly-initialized patches in an appropriately-sized grid.
**Scaling transformers for video generation**
Sora is a diffusion model[21](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-21), [22](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-22), [23](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-23), [24](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-24), [25](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-25); given input noisy patches (and conditioning information like text prompts), it’s trained to predict the original “clean” patches. Importantly, Sora is a diffusion *transformer*.[26](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-26) Transformers have demonstrated remarkable scaling properties across a variety of domains, including language modeling,[13](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-13), [14](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-14) computer vision,[15](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-15), [16](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-16), [17](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-17), [18](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-18) and image generation.[27](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-27), [28](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-28), [29](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-29)
In this work, we find that diffusion transformers scale effectively as video models as well. Below, we show a comparison of video samples with fixed seeds and inputs as training progresses. Sample quality improves markedly as training compute increases.Base compute4x compute32x compute
**Variable durations, resolutions, aspect ratios**
Past approaches to image and video generation typically resize, crop or trim videos to a standard size—e.g., 4 second videos at 256x256 resolution. We find that instead training on data at its native size provides several benefits.**Sampling flexibility**
Sora can sample widescreen 1920x1080p videos, vertical 1080x1920 videos and everything inbetween. This lets Sora create content for different devices directly at their native aspect ratios. It also lets us quickly prototype content at lower sizes before generating at full resolution—all with the same model.**Improved framing and composition**
We empirically find that training on videos at their native aspect ratios improves composition and framing. We compare Sora against a version of our model that crops all training videos to be square, which is common practice when training generative models. The model trained on square crops (left) sometimes generates videos where the subject is only partially in view. In comparison, videos from Sora (right) have improved framing.
**Language understanding**
Training text-to-video generation systems requires a large amount of videos with corresponding text captions. We apply the re-captioning technique introduced in DALL·E 3[30](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-30) to videos. We first train a highly descriptive captioner model and then use it to produce text captions for all videos in our training set. We find that training on highly descriptive video captions improves text fidelity as well as the overall quality of videos.
Similar to DALL·E 3, we also leverage GPT to turn short user prompts into longer detailed captions that are sent to the video model. This enables Sora to generate high quality videos that accurately follow user prompts.**a toy robota womanan old mana toy robotan adorable kangaroo**wearing**a green dress and a sun hatblue jeans and a white t-shirta green dress and a sun hatpurple overalls and cowboy boots**taking a pleasant stroll in**Johannesburg, South AfricaMumbai, IndiaJohannesburg, South AfricaAntarctica**during**a winter storma beautiful sunseta winter storma colorful festival
Prompting with images and videos**
All of the results above and in our [landing page](https://openai.com/index/sora/) show text-to-video samples. But Sora can also be prompted with other inputs, such as pre-existing images or video. This capability enables Sora to perform a wide range of image and video editing tasks—creating perfectly looping video, animating static images, extending videos forwards or backwards in time, etc.**Animating DALL·E images**
Sora is capable of generating videos provided an image and prompt as input. Below we show example videos generated based on DALL·E 2[31](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-31) and DALL·E 3[30](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-30) images.A Shiba Inu dog wearing a beret and black turtleneck.Monster Illustration in flat design style of a diverse family of monsters. The group includes a furry brown monster, a sleek black monster with antennas, a spotted green monster, and a tiny polka-dotted monster, all interacting in a playful environment.An image of a realistic cloud that spells “SORA”.In an ornate, historical hall, a massive tidal wave peaks and begins to crash. Two surfers, seizing the moment, skillfully navigate the face of the wave.**Extending generated videos**
Sora is also capable of extending videos, either forward or backward in time. Below are three videos that were all extended backward in time starting from a segment of a generated video. As a result, each of the three videos starts different from the others, yet all three videos lead to the same ending.00:0000:20
We can use this method to extend a video both forward and backward to produce a seamless infinite loop.**Video-to-video editing**
Diffusion models have enabled a plethora of methods for editing images and videos from text prompts. Below we apply one of these methods, SDEdit,[32](https://openai.com/index/video-generation-models-as-world-simulators/#citation-bottom-32) to Sora. This technique enables Sora to transform  the styles and environments of input videos zero-shot.Input video**change the setting to be in a lush junglechange the setting to the 1920s with an old school car. make sure to keep the red colormake it go underwaterchange the video setting to be different than a mountain? perhaps joshua tree?put the video in space with a rainbow roadkeep the video the same but make it be wintermake it in claymation animation stylerecreate in the style of a charcoal drawing, making sure to be black and whitechange the setting to be cyberpunkchange the video to a medieval thememake it have dinosaursrewrite the video in a pixel art styleConnecting videos**
We can also use Sora to gradually interpolate between two input videos, creating seamless transitions between videos with entirely different subjects and scene compositions. In the examples below, the videos in the center interpolate between the corresponding videos on the left and right.
**Image generation capabilities**
Sora is also capable of generating images. We do this by arranging patches of Gaussian noise in a spatial grid with a temporal extent of one frame. The model can generate images of variable sizes—up to 2048x2048 resolution.Close-up portrait shot of a woman in autumn, extreme detail, shallow depth of fieldVibrant coral reef teeming with colorful fish and sea creaturesDigital art of a young tiger under an apple tree in a matte painting style with gorgeous detailsA snowy mountain village with cozy cabins and a northern lights display, high detail and photorealistic dslr, 50mm f/1.2
**Emerging simulation capabilities**
We find that video models exhibit a number of interesting emergent capabilities when trained at scale. These capabilities enable Sora to simulate some aspects of people, animals and environments from the physical world. These properties emerge without any explicit inductive biases for 3D, objects, etc.—they are purely phenomena of scale.
**3D consistency.** Sora can generate videos with dynamic camera motion. As the camera shifts and rotates, people and scene elements move consistently through three-dimensional space.
**Long-range coherence and object permanence.** A significant challenge for video generation systems has been maintaining temporal consistency when sampling long videos. We find that Sora is often, though not always, able to effectively model both short- and long-range dependencies. For example, our model can persist people, animals and objects even when they are occluded or leave the frame. Likewise, it can generate multiple shots of the same character in a single sample, maintaining their appearance throughout the video.
**Interacting with the world.** Sora can sometimes simulate actions that affect the state of the world in simple ways. For example, a painter can leave new strokes along a canvas that persist over time, or a man can eat a burger and leave bite marks.
**Simulating digital worlds.** Sora is also able to simulate artificial processes–one example is video games. Sora can simultaneously control the player in Minecraft with a basic policy while also rendering the world and its dynamics in high fidelity. These capabilities can be elicited zero-shot by prompting Sora with captions mentioning “Minecraft.”
These capabilities suggest that continued scaling of video models is a promising path towards the development of highly-capable simulators of the physical and digital world, and the objects, animals and people that live within them.
**Discussion**
Sora currently exhibits numerous limitations as a simulator. For example, it does not accurately model the physics of many basic interactions, like glass shattering. Other interactions, like eating food, do not always yield correct changes in object state. We enumerate other common failure modes of the model—such as incoherencies that develop in long duration samples or spontaneous appearances of objects—in our [landing page](https://openai.com/index/sora/).
We believe the capabilities Sora has today demonstrate that continued scaling of video models is a promising path towards the development of capable simulators of the physical and digital world, and the objects, animals and people that live within them.
• [Milestone](https://openai.com/news/research/?tags=research-milestone)
• [Release](https://openai.com/news/research/?tags=research-release)
• [Sora](https://openai.com/news/research/?tags=model-sora)
• [Video Generation](https://openai.com/news/research/?tags=topic-video-generation)
**References**
1. 1Srivastava, Nitish, Elman Mansimov, and Ruslan Salakhudinov. "Unsupervised learning of video representations using lstms." International conference on machine learning. PMLR, 2015. 
2. 2Chiappa, Silvia, et al. "Recurrent environment simulators." arXiv preprint arXiv:1704.02254 (2017). 
3. 3Ha, David, and Jürgen Schmidhuber. "World models." arXiv preprint arXiv:1803.10122 (2018). 
4. 4Vondrick, Carl, Hamed Pirsiavash, and Antonio Torralba. "Generating videos with scene dynamics." Advances in neural information processing systems 29 (2016). 
5. 5Tulyakov, Sergey, et al. "Mocogan: Decomposing motion and content for video generation." Proceedings of the IEEE conference on computer vision and pattern recognition. 2018. 
6. 6Clark, Aidan, Jeff Donahue, and Karen Simonyan. "Adversarial video generation on complex datasets." arXiv preprint arXiv:1907.06571 (2019). 
7. 7Brooks, Tim, et al. "Generating long videos of dynamic scenes." Advances in Neural Information Processing Systems 35 (2022): 31769-31781. 
8. 8Yan, Wilson, et al. "Videogpt: Video generation using vq-vae and transformers." arXiv preprint arXiv:2104.10157 (2021). 
9. 9Wu, Chenfei, et al. "Nüwa: Visual synthesis pre-training for neural visual world creation." European conference on computer vision. Cham: Springer Nature Switzerland, 2022. 
10. 10Ho, Jonathan, et al. "Imagen video: High definition video generation with diffusion models." *arXiv preprint arXiv:2210.02303* (2022). 
11. 11Blattmann, Andreas, et al. "Align your latents: High-resolution video synthesis with latent diffusion models." Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition. 2023. 
12. 12Gupta, Agrim, et al. "Photorealistic video generation with diffusion models." arXiv preprint arXiv:2312.06662 (2023). 
13. 13Vaswani, Ashish, et al. "Attention is all you need." *Advances in neural information processing systems* 30 (2017). 
14. 14Brown, Tom, et al. "Language models are few-shot learners." *Advances in neural information processing systems* 33 (2020): 1877-1901. 
15. 15Dosovitskiy, Alexey, et al. "An image is worth 16x16 words: Transformers for image recognition at scale." *arXiv preprint arXiv:2010.11929* (2020). 
16. 16Arnab, Anurag, et al. "Vivit: A video vision transformer." *Proceedings of the IEEE/CVF international conference on computer vision*. 2021. 
17. 17He, Kaiming, et al. "Masked autoencoders are scalable vision learners." *Proceedings of the IEEE/CVF conference on computer vision and pattern recognition*. 2022. 
18. 18Dehghani, Mostafa, et al. "Patch n'Pack: NaViT, a Vision Transformer for any Aspect Ratio and Resolution." *arXiv preprint arXiv:2307.06304* (2023). 
19. 19Rombach, Robin, et al. "High-resolution image synthesis with latent diffusion models." *Proceedings of the IEEE/CVF conference on computer vision and pattern recognition*. 2022. 
20. 20Kingma, Diederik P., and Max Welling. "Auto-encoding variational bayes." *arXiv preprint arXiv:1312.6114* (2013). 
21. 21Sohl-Dickstein, Jascha, et al. "Deep unsupervised learning using nonequilibrium thermodynamics." *International conference on machine learning*. PMLR, 2015. 
22. 22Ho, Jonathan, Ajay Jain, and Pieter Abbeel. "Denoising diffusion probabilistic models." *Advances in neural information processing systems* 33 (2020): 6840-6851. 
23. 23Nichol, Alexander Quinn, and Prafulla Dhariwal. "Improved denoising diffusion probabilistic models." *International Conference on Machine Learning*. PMLR, 2021. 
24. 24Dhariwal, Prafulla, and Alexander Quinn Nichol. "Diffusion Models Beat GANs on Image Synthesis." *Advances in Neural Information Processing Systems*. 2021. 
25. 25Karras, Tero, et al. "Elucidating the design space of diffusion-based generative models." *Advances in Neural Information Processing Systems* 35 (2022): 26565-26577. 
26. 26Peebles, William, and Saining Xie. "Scalable diffusion models with transformers." *Proceedings of the IEEE/CVF International Conference on Computer Vision*. 2023. 
27. 27Chen, Mark, et al. "Generative pretraining from pixels." *International conference on machine learning*. PMLR, 2020. 
28. 28Ramesh, Aditya, et al. "Zero-shot text-to-image generation." *International Conference on Machine Learning*. PMLR, 2021. 
29. 29Yu, Jiahui, et al. "Scaling autoregressive models for content-rich text-to-image generation." *arXiv preprint arXiv:2206.10789* 2.3 (2022): 5. 
30. 30Betker, James, et al. "Improving image generation with better captions." *Computer Science. [https://cdn.openai.com/papers/dall-e-3*(opens in a new window)](https://cdn.openai.com/papers/dall-e-3)*. pdf* 2.3 (2023): 8 
31. 31Ramesh, Aditya, et al. "Hierarchical text-conditional image generation with clip latents." *arXiv preprint arXiv:2204.06125* 1.2 (2022): 3. 
32. 32Meng, Chenlin, et al. "Sdedit: Guided image synthesis and editing with stochastic differential equations." *arXiv preprint arXiv:2108.01073* (2021). **Authors**[Tim Brooks](https://openai.com/news/?author=tim-brooks#results), [Bill Peebles](https://openai.com/news/?author=bill-peebles#results), [Connor Holmes](https://openai.com/news/?author=connor-holmes#results), [Will DePue](https://openai.com/news/?author=will-depue#results), [Yufei Guo](https://openai.com/news/?author=yufei-guo#results), [Li Jing](https://openai.com/news/?author=li-jing#results), [David Schnurr](https://openai.com/news/?author=david-schnurr#results), [Joe Taylor](https://openai.com/news/?author=joe-taylor#results), [Troy Luhman](https://openai.com/news/?author=troy-luhman#results), [Eric Luhman](https://openai.com/news/?author=eric-luhman#results), [Clarence Ng](https://openai.com/news/?author=clarence-ng#results), [Ricky Wang](https://openai.com/news/?author=ricky-wang#results), [Aditya Ramesh](https://openai.com/news/?author=aditya-ramesh#results)
**Citation**
Please cite as Brooks, Peebles, et al., and use the following BibTeX for citation: https://openai.com/bibtex/videoworldsimulators2024.bib**Related articles**[View all Research](https://openai.com/news/research/)[Jul 10, 2024OpenAI and Los Alamos National Laboratory announce research partnership](https://openai.com/index/openai-and-los-alamos-national-laboratory-work-together/)[Jun 20, 2024Jun 20, 2024Jun 20, 2024Consistency ModelsConsistency ModelsConsistency Models](https://openai.com/index/consistency-models/)[Jun 20, 2024Jun 20, 2024Jun 20, 2024Improved Techniques for Training Consistency ModelsImproved Techniques for Training Consistency ModelsImproved Techniques for Training Consistency Models](https://openai.com/index/improved-techniques-for-training-consistency-models/)VoiceSpeed
• Ember
• Cove
• Juniper
• BreezeRead aloud

!https://images.ctfassets.net/kftzwdyauwt9/28bcbcb2-563a-432b-df938802863b/5fff5e2602331f7682792f5f541c75f9/young-tiger.jpg?w=3840&q=90&fm=webp

!https://images.ctfassets.net/kftzwdyauwt9/1d2955dd-9d05-4f33-13073dc9301d/8dc0bae8cb98054d083ab3cc3ade6859/figure-patches.png?w=3840&q=90&fm=webp

!https://images.ctfassets.net/kftzwdyauwt9/aa8b687c-bee5-4d72-c217057d28b6/756726b1b27a24c67d51a903c1b71e14/figure-diffusion.png?w=3840&q=90&fm=webp

!https://cdn.openai.com/tmp/s/prompting_0.png

!https://cdn.openai.com/tmp/s/prompting_2.png

!https://cdn.openai.com/tmp/s/prompting_4.png

!https://cdn.openai.com/tmp/s/prompting_6.png

!https://cdn.openai.com/tmp/s/image_0.png

!https://cdn.openai.com/tmp/s/image_1.png

!https://cdn.openai.com/tmp/s/image_2.png

!https://cdn.openai.com/tmp/s/image_3.png

!https://images.ctfassets.net/kftzwdyauwt9/3WF6oOv7cc50fCURi4NIJW/83606c8143b3a8b5b82508f290a4ef79/Los_Alamos.jpg?w=3840&q=90&fm=webp

!https://images.ctfassets.net/kftzwdyauwt9/50blkBs0y5oaeqXisv4UnE/33f02e3e4cebecb7391d237341bcc2d5/Consistency_Models.jpg?w=3840&q=90&fm=webp

!https://images.ctfassets.net/kftzwdyauwt9/6EOr5QVaaUgosoaauelMAg/4b9aedbbb0a7cce6654a922f523a638d/Improved_Techniques_for_Training_Consistency_Models.jpg?w=3840&q=90&fm=webp

Our research
• [Overview](https://openai.com/research/)
• [Index](https://openai.com/news/research/)Latest advancements
• [GPT-4](https://openai.com/index/gpt-4/)
• [DALL·E 3](https://openai.com/index/dall-e-3/)
• [Sora](https://openai.com/index/sora/)ChatGPT
• [For Everyone](https://openai.com/chatgpt/)
• [For Teams](https://openai.com/chatgpt/team/)
• [For Enterprises](https://openai.com/chatgpt/enterprise/)
• [ChatGPT login(opens in a new window)](https://chatgpt.com/)
• [Download](https://openai.com/chatgpt/download/)API
• [Platform overview](https://openai.com/api/)
• [Pricing](https://openai.com/api/pricing/)
• [Documentation(opens in a new window)](https://platform.openai.com/docs/introduction)
• [API login(opens in a new window)](https://platform.openai.com/login?launch)Explore more
• [OpenAI for business](https://openai.com/business/)
• [Stories](https://openai.com/news/stories/)Safety overview
• [Safety overview](https://openai.com/safety/)
• [Safety standards](https://openai.com/safety-standards/)Teams
• [Safety Systems](https://openai.com/safety-systems/)
• [Preparedness](https://openai.com/preparedness/)
• [Superalignment](https://openai.com/superalignment/)Company
• [About us](https://openai.com/about/)
• [News](https://openai.com/news/)
• [Our Charter](https://openai.com/charter/)
• [Security](https://openai.com/security-and-privacy/)
• [Residency](https://openai.com/residency/)
• [Careers](https://openai.com/careers/)Terms & policies
• [Terms of use](https://openai.com/policies/terms-of-use/)
• [Privacy policy](https://openai.com/policies/privacy-policy/)
• [Brand guidelines](https://openai.com/brand/)
• [Other policies](https://openai.com/policies/)OpenAI © 2015–2024Manage Cookies[(opens in a new window)](https://x.com/OpenAI)[(opens in a new window)](https://www.youtube.com/OpenAI)[(opens in a new window)](https://www.linkedin.com/company/openai)[(opens in a new window)](https://github.com/openai)[(opens in a new window)](https://www.instagram.com/openai/?hl=en)[(opens in a new window)](https://www.tiktok.com/@openai?lang=en)[(opens in a new window)](https://discord.gg/openai)

Video generation models as world simulators | OpenAI