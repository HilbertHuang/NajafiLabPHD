# Two-photon Ch2 video visualizer

This small command-line tool finds all `Ch2` TIFF stacks below a raw-data
directory, sorts and joins them in acquisition order, and stops at the requested
duration. It reads the acquisition frame rate from TIFF/OME metadata. For split
Prairie OME-TIFF exports whose referenced companion metadata was not copied, it
derives the rate from consecutive stack sizes and preserved completion times.

The output is a 60 fps MP4 of the entire field of view. It uses a green fluorescence palette,
automatic 1st–99.8th percentile contrast, and linear frame interpolation or
dropping as needed. Denoising uses mild spatial Gaussian smoothing.

## Install and run

```powershell
python -m pip install -r requirements.txt
python visualize_2p.py data --duration 30 --rolling-average 8 --speed 1
```

The same command with one argument per line:

```powershell
python visualize_2p.py data `
  --duration 30 `
  --rolling-average 1 `
  --speed 1
```

`--rolling-average` defaults to `1`, which applies no temporal averaging.

## Project structure

- `visualize_2p.py`: small command-line entry point.
- `two_photon/cli.py`: arguments and validation.
- `two_photon/tiff.py`: TIFF discovery, metadata, and frame streaming.
- `two_photon/video.py`: denoising, contrast, resampling, and MP4 encoding.

Output:

- `output/fov_ch2.mp4`

If fewer frames are available than requested, all available frames are used.
TIFF files must contain `Ch2` in their names. Timing may be supplied by OME
`TimeIncrement`, OME `Plane DeltaT`, `framePeriod`, `frameRate`, or `fps` metadata;
multiple split Prairie stacks can instead use their preserved file timestamps.
