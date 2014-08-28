---
layout: post
date: 2014-08-22 0:15:30 UTC
title: Significant science, gelation and phase separation
---

# Gelation and phase separation in attractive colloids

After five weeks of data collection, and a significant effort in [organizing]({% post_url 2014-07-15-whereis_data %}), [stabilizing and processing]({% post_url 2014-08-21-stabilize_images %}) the data from the ACE-M2 experiment, we have our **first significant scientific result**. 

## Ground-based experiments and their limitations

In a long series of experiments here at Harvard and on the ISS with BCAT, I have explored the roles of gelation and phase separation in driving the behavior of attractive colloidal systems. We have published a number of papers on the subject, most notably a Letter in _Nature_ that was the core of my PhD thesis, a publication that now has several hundred citations. This manuscript, accompanying video and other resources can be found on [here](http://www.peterlu.org/content/physics-attractive-colloids).

The major conclusion in this work is that gelation, heretofore considered a purely kinetic process, is actually driven by thermodynamics, specifically an instability called spinodal decomposition. This drives separation of these attractive colloid-polymer mixtures into two phases, a colloid-rich phase and a colloid-poor phase. The density of the colloid-rich phase, plus the attractions between the particles, causes it to arrest into a solid---what we term a gel. We observed no significant structural changes on the order of 100,000 seconds, or roughly a day, using confocal microscopy to gain a full-3D view on the single-particle level.

There are several limitations that prevent looking at these gel structures for significantly longer. First, it is difficult to maintain a stable environment in the lab for weeks at a time---or at least it was more difficult in the past before the HVAC system was upgraded---with significant (5+ degree) fluctuations in room temperature. Even with good laboratory air-based temperature control, there are bound to be fluctuations, which lead to density differences and therefore gravity-induced shear stresses on the sample. Secondarily, the samples in those experiments were made by sealing colloid-polymer mixtures in glass capillaries with 5-minute epoxy. This works just fine over a few days, but tends to allow leakage over timescales of weeks or longer.

### The advantages of microgravity and ISS, and the importance of fundamental experiments like ACE-M2

Going to a stable microgravity environment aboard the ISS with an apparatus like the LMM solves a number of those problems. The sample chambers don't leak after initial sealing, as evidenced by the stability in the size of the air bubbles. Should any amount of these volatile solvents escape, they can only be replaced with air, manifest as a growing bubble---which we do not observe.

Even more significantly, the microgravity environment of the ISS prevents significant sedimentation, improving the best density-match we can produce on earth by several orders of magnitude. With good sample preparation and no significant temperature control (i.e. normal room-temperature variation), the particles and solvent can have a density matched to about 1 part in a thousand. Adding the six orders of magnitude improvement in so-called "microgravity" gives an environment with these particles that is, by analogy, in the "nanogravity" regime. Therefore, we really, really don't expect sedimentation to play a role in these samples.

Why does this matter? it is possible that the gel structures are arrested by a combination of internal stresses and shear stress created by gravity. Yes, we have done everything to minimize it on earth, and particularly if there are random fluctuations in temperature around a point of equal density, fluctuations should cause particles to rise just as much as they might fall. But a definitive test would allow observation of these gel structures for far longer.

## Gelation observed in microgravity

ACE-M2 in the LMM aboard the ISS gives us that opportunity. Just as in the laboratory experiments, the astronaut crew homogenize the samples by mixing with a magnet. We then observe the colloid-polymer structure over time with microscopy. This particular sample, [number 22]({% post_url 2014-03-05-ace_m2_samples %}) (platter 2105, strip E-618, well 4), was observed to form a gel on earth, before its launch into space. By looking at the highest magnification that generates good images, 40x, we can look in detail at the structures that form. Here, we see a network that forms and slightly fluctuates, but does not undergo significant topological rearrangement over the course of four weeks:

![sample 22 gel](/images/2014_08_22_gelation_phase_sep/p5e4s22_40x_z060_xyd_2wks_sm.gif)

![](/images/ace_m2_sample_tiles/sample22.png)

This movie has three images, collected 60 microns in from the cover slip, from calendar days 198, 209 and 223, corresponding to days 4, 18 and 32 of the experiment. As before, the frames are spaced by 0.5 seconds in the movie, with a 2-second delay at the end, before it repeats (infinitely). The features are basically the same, oscillating slightly (and the plane of focus will have fluctuated a little bit, as well). But overall there are no significant movements through the movies---which again represents four weeks---even though the features are on the scale of just a few microns. 

## Spinodal decomposition / phase separation observed in microgravity

We also launched a sample, [number 21]({% post_url 2014-03-05-ace_m2_samples %}), (platter 2105, strip E-618, well 5), that we observed on earth to undergo equilibrium phase separation, ultimately to completion. That is, we did not see the arrest of the structure that we do in the sample that gels. These two samples use the same particles in roughly the same concentration, and the only difference is the polymer concentration, which is higher in the one that gels. On orbit, we see a slow evolution of phase separation, a coarsening that we observe in BCAT, but on a much smaller leength scale:

![sample 22 gel](/images/2014_08_22_gelation_phase_sep/p5e5s21_40x_z060_xyk_2wks_sm.gif)

![](/images/ace_m2_sample_tiles/sample21.png)

Note that the phase-separating sample coarsens throughout: the bright parts get fatter, indicating that the network structure is swelling, and does not show any obvious signs of stopping.

## A significant and fundamental scientific observation

The images in the two movies were taken under as close to identical conditions as possible: same particles in similar concentrations (about 21% volume fraction), loaded in to the same sample strip (E-618) on the same platter (2105). They were imaged at the same time (days 4, 18 and 32 after mixing) with the same microscope objective (40x magnification) at the same depth from the cover slip (60 microns). 

The only difference is the polymer concentration: the sample that gels has about twice as much polymer as the one that undergoes equilibrium phase separation. Moreover, we also launched a sample that has similar particles at similar concentration _but no polymer_, [number 10]({% post_url 2014-03-05-ace_m2_samples %}) (platter 2104, strip B-615, well 4); it does not undergo phase separation, but instead shows no structure and remains homogeneous.

What we have then, is an indicator of phase behavior for these samples with 20% colloid volume fraction, and a polymer whose radius is a third that of the particles. This is much longer than the relatively small particle used in the ground studies, where all samples that phase separated underwent gelation. Here, we see:

1. Zero polymer (sample 10): no phase separation, sample remains homogeneous.

2. Low polymer (sample 22): slow phase separation, no arrest.

3. High polymer (sample 21): gelation / arrested phase separation, no evolution.

**This is a the first definitive observation of this phemonenon, particularly over such long timescales (well in excess of an order of magnitude longer than what has been done reliably on earth!**

## Practical implications

One of the major goals of the NASA-sponsored colloids research is having an impact here on earth, and in the preceding experiment ACE-M1 looked at the behavior of another attractive colloid system by Procter and Gamble. Here, we are understanding the fundamental physical behavior of systems that model how a number of economically important products may behave on the shelf, such as Downy fabric softener, a billion-dollar product sold worldwide. What the ISS experiments allow us it to deconstruct and simplify the systems in to their essential components, looking at the fundamental phase-separation and gelation behavior without gravity, and with close observation. Whether or not a product remains stable on the shelf is an important issue in all sorts of consumer products, foods, etc. and destabilization is largely driven by phase separation---which itself may be stopped by the process of gelation. Knowing how to predict this behavior ahead of time can have significant economic consequences on how products are formulated and designed. Thus, the results we have found in ACE-M2 provide insight that might ultimately help companies like Procter and Gamble to make better products, increasing employment and growing the American economy in the future.
