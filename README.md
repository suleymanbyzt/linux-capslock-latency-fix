
# INstant CAps LOck FIx for LInux (X11)

By default, Caps Lock on Linux (X11 sessions) is only activated when you **release the key**, not when you press it.  
This small tweak changes that behavior so Caps Lock toggles **immediately on key press**, just like in Windows or macOS.

---

## What this does

- Makes `Caps Lock` respond instantly when you press it
- Applies automatically when you log in
- Works with GNOME, XFCE, etc.
- No sudo/root needed

---

## How to set it up

### 1. Export your current keymap:
```bash
xkbcomp -xkb $DISPLAY ~/xkbmap
```

### 2. Edit the `xkbmap` file:
```bash
nano ~/xkbmap
```

Find this line:
```
key <CAPS> { [ Caps_Lock ] };
```

Replace it with:
```xkb
key <CAPS> { 
    repeat=no,
    type[group1]="ALPHABETIC", 
    symbols[group1]=[ Caps_Lock, Caps_Lock ],
    actions[group1]=[
        LockMods(modifiers=Lock), 
        Private(type=3,data[0]=1,data[1]=3,data[2]=3)
    ] 
};
```

Save and close.

### 3. Create an autoload script:
```bash
nano ~/.config/apply-caps-fix.sh
```

Paste:
```bash
#!/bin/bash
sleep 2
if [ -n "$DISPLAY" ]; then
    xkbcomp -w 0 /home/$USER/xkbmap $DISPLAY
    echo "CapsFix ran at $(date)" >> /tmp/capsfix.log
fi
```

Make it executable:
```bash
chmod +x ~/.config/apply-caps-fix.sh
```

Optional: test it right away (without reboot):
```bash
~/.config/apply-caps-fix.sh
```

### 4. Run the script on login using `.xprofile`:
```bash
nano ~/.xprofile
```

Paste this:
```bash
~/.config/apply-caps-fix.sh
```

Make sure `.xprofile` is executable:
```bash
chmod +x ~/.xprofile
```

---

## Reboot and test

After reboot, press Caps Lock — it should toggle **instantly**.  
You can also check the log file:
```bash
cat /tmp/capsfix.log
```

---

##  Want to remove it?

Just delete:

- `~/xkbmap`
- `~/.config/apply-caps-fix.sh`
- `~/.xprofile`

Then reboot.
