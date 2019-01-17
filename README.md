# CutBucket
Author: Caleb Fink

This repo contains tools for collaborators to share boolean arrays between machines for collaborative data analysis. It also provides version control for arrays. When an array is saved, if an array with the same name already exists, the older version is saved in an archive directory and given a version name. 


To use the module to share arrays between collaborators, you must create a GitHub repository to hold the arrays and set it up with ssh (instruction below), then give all of your collaborators access to this repo. 

An example of how to use the module is shown below. Please also make sure that you are able to run the demo in the ```demos/``` folder.

Note, use of this module will require a basic understanding of ```git``` in order trouble shoot any issues that may arrise. 

Please email cwfink@berkeley.edu if you have any issues.


### Demo of cutbucket.CutUtils saving and loading structure

Author: Caleb Fink


```python
import numpy as np
from cutbucket import CutUtils 
```

**create CutUtils Object**

By default CutUtils will connect with the GitHub repo where it is located.

For information on how to set up github repository for use with SSH, see:

https://help.github.com/articles/changing-a-remote-s-url/#switching-remote-urls-from-https-to-ssh

and to set up SSH key with GitHub acount:

https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/#platform-linux


If the directory 'test_cuts/' does not yet exist, it will be created, along with 'test_cuts/current_cuts/' and 'test_cuts/archived_cuts/'





```python
repopath = '/scratch/cwfink/repositories/analysis_test_cuts/'
```


```python
cuts = CutUtils(repopath = repopath, relativepath = 'test_cuts/', lgcsync = True)
```

    folder: /test_cuts/current_cuts/ does not exist, it is being created now
    folder: /test_cuts/archived_cuts/ does not exist, it is being created now
    Connecting to GitHub Repository, please ensure that your ssh keys have been uploaded to GitHub account


**Make some cuts**


```python
ctest = np.ones(shape = 100, dtype = bool)
```

**Save new cut**


```python
cuts.savecut(ctest, name='ctest', description='this is a really stupid test cut')
```

    No existing version of cut: ctest. 
     Saving cut: ctest, to directory: /scratch/cwfink/repositories/analysis_test_cuts/test_cuts/current_cuts/
    syncing new cut with GitHub repo...


**Make a new cut and try to over write previously saved test**


```python
cnewtest = np.ones(shape = 100, dtype = bool)
cnewtest[:4] = False
cuts.savecut(cnewtest, name='ctest', description='this is another stupid test cut')
```

    updating cut: ctest in directory: /scratch/cwfink/repositories/analysis_test_cuts/test_cuts/current_cuts/ and achiving old version
    syncing new cut with GitHub repo...
    old cut is saved as: /scratch/cwfink/repositories/analysis_test_cuts/test_cuts/archived_cuts/ctest_v0.npz
    syncing old cut with GitHub repo...


**Make a few more cuts just to populate directories**


```python
cnewtest1 = np.ones(shape = 100, dtype = bool)
cnewtest1[1:2] = False
cnewtest2 = np.ones(shape = 100, dtype = bool)
cnewtest2[3:44] = False
cnewtest3 = np.ones(shape = 100, dtype = bool)
cnewtest3[:99] = False


cuts.savecut(cnewtest1, name='csillytest', description='this is a silly test cut')
cuts.savecut(cnewtest2, name='creallysillytest', description='this is a really silly test cut')
cuts.savecut(cnewtest3, name='ctest', description='this is stupid')
```

    No existing version of cut: csillytest. 
     Saving cut: csillytest, to directory: /scratch/cwfink/repositories/analysis_test_cuts/test_cuts/current_cuts/
    syncing new cut with GitHub repo...
    No existing version of cut: creallysillytest. 
     Saving cut: creallysillytest, to directory: /scratch/cwfink/repositories/analysis_test_cuts/test_cuts/current_cuts/
    syncing new cut with GitHub repo...
    updating cut: ctest in directory: /scratch/cwfink/repositories/analysis_test_cuts/test_cuts/current_cuts/ and achiving old version
    syncing new cut with GitHub repo...
    old cut is saved as: /scratch/cwfink/repositories/analysis_test_cuts/test_cuts/archived_cuts/ctest_v1.npz
    syncing old cut with GitHub repo...


**List the names of all the current cuts**


```python
cuts.listcuts(whichcuts='current')
```




    ['creallysillytest', 'csillytest', 'ctest']



**List the names of all the archived cuts**


```python
cuts.listcuts(whichcuts='archived')
```




    ['ctest_v1', 'ctest_v0']



**Let's reload a test and make sure it is the same as the cut we have defined in the namespace**


```python
ctest_reload = cuts.loadcut('ctest', lgccurrent=True)
if np.array_equal(cnewtest3, ctest_reload):
    print('The arrays are the same!')
```

    The arrays are the same!


**Now let's load the cut description for the current version of** ```ctest```


```python
print(cuts.loadcutdescription('ctest', lgccurrent=True))
```

    this is stupid


^ that sounds about right

**We can also add all the current cuts into the namespace by using the** `self.updatecuts()` **function**

Doing this is perhaps not the most python thing to do, but it will ensure that you have the most recent cuts defined in your namespace when working with collaborators


```python
exec(cuts.updatecuts())

```

    The following cuts will be loaded into the namespace of cuts.py: ['creallysillytest', 'csillytest', 'ctest'] 
     make sure to run exec()             on the return of this function to import them into the local namespace


If this demo ran without any errors, then you have probably set up your GitHub account properly. If you encountered any errors, then you probably didn't... 



