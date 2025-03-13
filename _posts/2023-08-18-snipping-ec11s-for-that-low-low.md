---
title: Putting low profile back in to ec11 encoders
date: 2023-08-18
toc: true
mermaid: true
categories: ["Mechanical Keyboards"]
tags: ["customization"]
---

One of the greatest joys of having a 3d printer is finding problems (that are not really problems) and fixing them by prototyping your own solution. Designing a model, printing it and testing it through multiple iterations can quickly swallow up a weekend's worth of project allowance, but the results are worth it. Or at least, that's what I tell myself when I forget to do the dishes because I'm recalibrating the printing bed for the third time today.

Earlier this year, I built a Lily 58 from [SplitKB](https://splitkb.com) → [Autota Lily58](https://splitkb.com/collections/keyboard-kits/products/aurora-lily58). I built it with choc switches to keep it low profile, and I wanted to include 2 encoders on this board that still had push functionality. For this reason, I opted for EC11 encoders instead of the more low-profile EC12 encoders. The only main downside of this is that even the shortest [EC11 encoder sticks out like a sore thumb](/assets/images/ec11encoder/high_boii.jpeg) and adding a big knob on top of it only made it worse.

Time for a cut!

# Designing the knob

I started by learning how to create a knob in Fusion 360. I had never created anything from scratch before, so I was lucky that there was an [easy-to-follow tutorial on YouTube](https://www.youtube.com/watch?v=L-BHRdHFmdg). I also learned how to [add a knurling pattern to the knob](https://www.youtube.com/watch?v=CXqHa3tIPiA). With this, I created the first version of my knob:

![First design of knob](/assets/images/ec11encoder/first_design.png)
_Get it here https://thangs.com/mythangs/file/920412_

But this was not the Low Low I was hoping for. I previously looked for hours on AliExpress for low profile encoders, but the lowest I could find was still around 10mm. So! Back to designing. I started out by designing a different version of the knob. Some open, some with dimples:

![Prototypes](/assets/images/ec11encoder/prototypes.jpeg)
_Prototypes of knobs_

Eventually I created a open version of the knop. With the idea of gluing a cover on top. But then I realized that was stupid, so I redesigned it. The nice thing about the open version is that it can be used as a cutting helper.

# Installing and trimming

1. Print the knob(s), get the model at [Thangs](https://thangs.com/mythangs/file/920416?source=All+Files&activeBottomTab=files)
2. Print the cutting help model. It's called "Rotary Encoder Low Profile - Base.stl" and is part of the model at [Thangs](https://thangs.com/mythangs/file/920416?source=All+Files&activeBottomTab=files)
3. Install the helper [Helper Installed](/assets/images/ec11encoder/cuttting_helper.jpeg)
4. Snip a de snip (be carefull, this part can fly away. Cut it in a bag or wear glasses when trimming the encoder) [Snipping](/assets/images/ec11encoder/snipping.jpeg)
5. Carefully remove the cutting help from the stem by wiggeliging it loose
6. File down the cut [File](/assets/images/ec11encoder/filling_down.jpeg)
7. Install the new knob. This might take a few tries but with some patiance it should fit snug on the encoder. [Installing knob](/assets/images/ec11encoder/low_encoder_view.jpeg)

# Results

![Results](/assets/images/ec11encoder/low_encoder.jpeg)

I'm really happy with how this turned out. My keyboard is "more low profile" now, and it's easier to transport without the knob sticking out. This project also gave me some 3D modeling experience, of which I had none. Onward and upward!

