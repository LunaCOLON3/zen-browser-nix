> # this project is now archived
>
> im not sure if this project really needs a whole announcement for archival because its pretty simple but its the first thing i made that had any practical use and im proud of that. however, i no longer use zen as my primary browser, so it feels silly to keep this up. i also intend to move to [codeberg](https://codeberg.org/LunaCOLON3) for future projects because [github kinda sucks](https://giveupgithub.org/). anyway, thanks to everyone who used flake. its mostly just code taken from other places with tiny little changes but im still proud of it and im glad i could help some people <3
>
> if you still use this, heres a much better one thats actually properly maintained:
> https://github.com/0xc000022070/zen-browser-flake

# Zen Browser Nix
### NixOS and Home Manager modules for Zen Browser

Basically just the default `programs.firefox` but the default package is the Zen browser. I also added an overlay so you can just use the package if you would prefer.

Thank you so much to [youwen5](https://github.com/youwen5) for making [the zen-browser package I used](https://github.com/youwen5/zen-browser-flake). I tried to use several other packages but this is the only one that was built similar enough to Firefox to work.

## Installation:
Simply add this flake as an input and import the module.
```nix
# flake.nix
{
  inputs = {
    nixpkgs.url = "nixpkgs/nixos-unstable";
    zen-browser = {
      url = "github:LunaCOLON3/zen-browser-nix";
      inputs.nixpkgs.follows = "nixpkgs";
    home-manager = {
      url = "github:nix-community/home-manager";
      inputs.nixpkgs.follows = "nixpkgs";
    };
  };

  outputs = { nixpkgs, catppuccin, home-manager }: {
    # for nixos module home-manager installations
    nixosConfigurations.nixos = pkgs.lib.nixosSystem {
      system = "x86_64-linux";
      modules = [
        ./configuration.nix
        zen-browser.nixosModules.zen-browser
        # if you use home-manager
        home-manager.nixosModules.home-manager

        {
          # if you use home-manager
          home-manager.users.luna = {
            imports = [
              ./home.nix
              zen-browser.homeManagerModules.zen-browser
            ];
          };
        }
      ];
    };

    # for standalone home-manager installations
    homeConfigurations.luna = home-manager.lib.homeManagerConfiguration {
      pkgs = nixpkgs.legacyPackages.x86_64-linux;
      modules = [
        ./home.nix
        zen-browser.homeManagerModules.zen-browser
      ];
    };
  };
}
```

Then just use the `programs.zen-browser` option in your NixOS or Home Manager configuration. It is almost identical to `programs.firefox` so most Firefox configurations should work without any changes.

```nix
{ config, pkgs, lib, ... }:
{
  # [...]
  programs.zen-browser.enable = true;
}
```

Or if you would rather use the overlay, add `zen-browser.overlay` to `nixpkgs.overlays` and install `pkgs.zen-browser`.

```nix
{ config, pkgs, lib, inputs, ... }:
{
  # [...]
  nixpkgs.overlays = [ inputs.zen-browser.overlay ];

  # for nixos
  environment.systemPackages = [
    pkgs.zen-browser
  ];

  # for home-manager
  home.packages = [
    pkgs.zen-browser
  ];
}
```
