# Model Import

- Import the model in the project folder **Asset/Meshes/Map/[LocationName]**
- Have a look at the other models (PortoCervo for instance) to use the same options.
- From the model, create a prefab with the final name. (it need to be same as the '_id' choosen for the GS Database so no spaces ,no uppercase, no special characters).
- Add the following components to the prefab:
    - DecorColorization: for colors presets based on time of day
    - DecorPlacement, adjust accordingly 
        - The global position and scaling. 
        - Used for the marina position and rotation (The decor is loaded in the marina scene after a race in this location).
- For the introduction camera:
    - Setup a child with a DecorPanorama component and a CinemachineSmoothPath
    - In the CinemachineSmoothPath, set the path points to go around the decor nicely (or ask a graphist)
    - In doubt, check the other decor prefabs.
- Finally, set the assetbundle name for the prefab with the prefix "decor_", for instance "decor_honolulu"

# Decor images
The decor requires 4 images (provided by the graphist) to be added in the asset bundle decor_imgs.
- A small image for the lobby. Ex: _honolulu_
- A large image for the lobby. Ex: _honolulu-2_
- A flag image if not already present using the IOC code with the prefix "flag-", it need to be put in the folder "Assets/Resources/Textures/MapFlags"
- An image containing the new decor's name for sailGP boundaries 
    - Put it in project's folder: Assets/Textures/Gameplay/Boundaries_Logos
    - In the Gameplay scene, search for raceBoundaries and add the Sprite the the logo library list

You can have an overview of all the AssetBundles in Window > AssetBundleBrowser

# Gamesparks setup
Add the entry in the database _script.location_:

```jsonc
{
  "_id": "portocervo", //name of the assetbundle is decor_portocervo + used in the decor images
  "name": "PortoCervo", //name of the prefab
  "country": { // for display of flag mostly
    "_id": "IT",
    "IOC": "ITA",
    "name": "Italy"
  },
  "timezone": 1 //used for the colorization based on time of day
}
```