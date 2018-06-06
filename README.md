# CS472-Lab
Architecture woo

_To implement level-3 caching in SimpleScalar's sim-cache, all that is needed is some vim skills, or some patience._
Really what we're doing is copy-pasting the L2 code and modifying it to be "L3", renaming stuff and re-routing other to the newly-named stuff.

Here's a step-by-step overview of what I did:

**1. Enable L3 caching.**
  - Create new structs for the l3 objects
  
    (VIM)
    `/static struct cache_t *cache_il2↵` `yy` `p`
    `/static struct cache_t *cache_il2↵` `n` `$` `r` `3`
  - Create new functions for l3 simulations and modify L2 functions to allow use of L3 caching. Example for data cache:
    
    (VIM)
    `/dl1_access_fn↵` `kk` `v20↵` `$` `y` `k` `p` Copies our function skeleton from the implementation of the L1 data cache
    
    `v20↵` `$` `:s/dl2/dl3/g↵` `$` Replaces dl2 with dl3
    
    `v20-` `$` `:s/dl1/dl2/g↵` Replaces dl1 with dl2
    
    `:1` `/dl2_access_fn↵` `n` `kk` `v3↵` `$` Finds old L2 function to become the L3 function, since L3 will take over what used to be L2's responsibility -- accessing system memory.
    
    `:s/dl2/dl3/g↵` `$` Replaces dl2 with dl3    
    
    In layman's terms, we just re-routed the L2 function to point to L3 memory, exactly like L1 points to L2. Then we have to make a new function for L3, but it behaves just like the L2 function, so we simply change the name of the old L2 function to be L3.


**2. Enable sim-cache parameters for L3.**
  - Instantiate option variables:
  
    (VIM)
    `/static char *cache_dl2_opt↵` `yy` `p` `v$` `:s/dl2/dl3/↵` Finds and copies our variable definition. Do again for instruction cache.
    
  - Register option items:
  
    (VIM)
    `/opt_reg_string↵` Finds the option register string function. I'm not including all the vim here but the idea is to add options for L3 which mirror those of L2. Once again, it's just a matter of copy-pasting and changing names.
    
    
**3. OPTONAL: Handle errors**
   I'm not including any vim here because this part is tricky. Look for `strcmp` and `mystricmp` -- they check for the presence of variables. Here, we can check which options are set. Options must be set according to the rules of the system (i.e. L1->L2->L3 or L1->L3 and a prerequisite level to L2 or L3 is required).
   Again, this just requires some simple copypasta and refactoring.
