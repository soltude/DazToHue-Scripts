Loading a starting ROM
For this example, in addition to the Golden Palace 9 controls, I also want the character to have the joint corrective morphs as well as the face controls, so to start with I’m going to load the G9 LINEAR JCM FAC example ROMs onto the character.

Navigate to where the DazToHue pose presets are.
Make sure you are on frame 0 of the animation timeline.
Make sure that you have the base figure selected in the scene outliner.
Hold down the CTRL key on your keyboard and double-click the G9 LINEAR JCM FAC - Base preset.
In the dialog box that appears, select the option to Turn Off Limits, then click the Accept button.
Make sure you are still on frame 0 of the animation timeline.
Select the Genesis 9 Mouth figure in the scene outliner.
Hold down the CTRL key on your keyboard and double-click the G9 LINEAR JCM FAC - Mouth preset.
In the dialog box that appears, select the option to Turn Off Limits, then click the Accept button.
Appending the Golden Palace 9 ROM
With our starting ROM loaded, our timeline now runs from frame 0 to frame 616 giving us a total of 617 frames. Now we want to append the Golden Palace 9 ROM to the end of our timeline.
Start by adding one additional frame to the timeline by changing the value of the total frames from 617 to 618.

Make sure you are on frame 0 of the timeline and that you have the Base figure selected in the scene outliner.
Select Edit -> Figure -> Memorize -> Memorise Figure.
Now go to the last frame which in this example would be frame 617 and still with the Base figure selected in the scene outliner, select Edit -> Figure -> Restore -> Restore Figure.
Now, still on frame 617 select the Golden Palace G9 figure in the scene outliner.
Double-click the GP9 - Golden Palace preset.
At this point we’ve now loaded the example Golden Palace 9 ROM onto our character and our animation timeline has now increased to 721 frames.

You might have noticed that the default shaping you applied to Golden Palace 9 in step 2 may have gone when viewing frame 617. This is where things can get a little tricky.
Unlike the JCM and FAC ROMs, with the Golden Palace 9 ROM you will need to art-direct the parameters on some of the ROM frames to get the result you’re after. This is due to the infinite shaping possibilities of Golden Palace 9. 
However, I’ve introduced the concept of Fence frames to try to make things a little easier for you. A Fence frame is simply a copy of a figure’s shape and pose. They help to greatly reduce the chance of unwanted drifting between frames of the ROM and allow you to work on small sections of the ROM which need some art direction. In this case, the Fence frames are just a copy of the figure in its default starting shape and pose.
The Golden Palace 9 ROM is broken up into different sections, with a Fence frame in between each section. I’ve put together this spreadsheet to help you understand the different sections as well as where the Fence frames are located.
The frame numbers in the spreadsheet assume that the starting frame for the Golden Palace 9 section of our ROM is frame 617, as in this example. If your starting ROM was different you will need to offset the frame numbers in the spreadsheet.
TIP: You can save your own copy of this spreadsheet into your own Google Sheets account so that you can modify the frame numbers for your own purposes.
Setting the fence frames
First, let’s apply the first of the Fence frames.
Make sure you are on frame 0 of the timeline and that you have the Golden Palace 9 figure selected in the scene outliner.
Select Edit -> Figure -> Memorize -> Memorise Figure.

Now go to the first Fence frame of your Golden Palace 9 ROM which in this example would be frame 617.
Now, still with the Golden Palace 9 figure selected in the scene outliner, select Edit -> Figure -> Restore -> Restore Figure.

Now, repeat step 22 for each of the remaining Fence frames which in this example would be frames 712, 716 and 719 as indicated in the spreadsheet.
Finally, set all of the keyframes to linear interpolation.
In the timeline pane of Daz Studio, expand the base figure node and click on it.
Hold down the SHIFT key and click on the last node to select them all.
Right-click on base figure node and select Select -> Select All Item Keys (Filtered)
The keys in the timeline should turn yellow.
Now, click on the Interpolation Type button and select Linear.

At this stage you should now be able to scrub through the timeline and hopefully the character’s vagina should maintain its base shape throughout.
Art-directing the Golden Palace ROM
Referring again to the spreadsheet, we can see in the comments column that some of the frames of the Golden Palace 9 ROM require art direction from you in order to get the desired results and others you can optionally art direct if you wish.

TIP: You may find it easier to work if you zoom in on the range of the timeline which relates to Golden Palace 9.

The first sections which you can optionally art-direct are frames 713, 714 and 715 which are for Vagina open, Vagina Squeeze and Vagina Stretch respectively. Again, you can optionally art-direct these poses beyond what is included by default.
If you decide that you want to alter some of the Golden Palace 9 parameters for these poses, remember that you will want to zero out its value on the frames either side of the current frame to maintain that sawtooth interpolation pattern that is described in the Custom ROM Guide.
The next section, frames 717 and 718, requires art-direction as there are no keyframes set by default. These two frames, or poses, relate to the Anus Open and Anus Contraction poses. 
On frame 717 use any combination of GP9 anus parameters to have the anus opening to the maximum extent according to what you want.
On frame 718 use any combination of GP9 anus parameters to have the anus contracting or clenching.
Finally, frame 720 relates to the Clitorus Erect pose. Again, you can optionally art-direct this pose to meet your own requirements.
IMPORTANT: When changing parameters for any pose remember to zero out the values on the frames either side of the frame you’re working on to maintain the usual sawtooth pattern.
Once you’ve finished art-directing your Golden Palace 9 ROM you’ll want to set all of the keyframes to linear one last time by repeating steps 24 to 28.
