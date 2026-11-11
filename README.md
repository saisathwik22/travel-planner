# travel-planner


## How to run ?

1. Create the virtual environment

```
python -m venv travel
```

2. Activate environment

```
.\travel\Scripts\Activate.ps1
```

3. Install the requirements


Before installing actual packages, dry run to check if any conflicts are occuring with package versions.
It only simulates the installation.

```
pip install --dry-run -r requirements.txt
```

If everything has run smoothly, then run below command to actually install packages.

```
python -m pip install -r requirements.txt
```