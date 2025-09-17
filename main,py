import os, json, sys
import pyperclip

OUTPUT_ROOT = "output"

def write_directory(root: str, out_file: str="directory.txt"):
    entries = []
    for dirpath, dirnames, filenames in os.walk(root):
        rel = os.path.relpath(dirpath, root)
        if rel == ".":
            base = ""
        else:
            entries.append(f"folder: {rel}")
            base = rel + os.sep
        for f in sorted(filenames):
            entries.append(f"{base}{f}")
    with open(os.path.join(root, out_file), "w", encoding="utf-8") as fp:
        fp.write("\n".join(entries))
    print(f"→ [{root}/{out_file}] updated")

def run_commands(cmds):
    for c in cmds:
        t = c.get("type")
        if t == "cd":
            to = c["to"]
            print(f"→ cd {to}")
            os.chdir(to)
        elif t == "mkdir":
            path = c["path"]
            print(f"→ mkdir -p {path}")
            os.makedirs(path, exist_ok=True)
        elif t == "rmdir":
            path = c["path"]
            print(f"→ rmdir {path}")
            os.rmdir(path)
        elif t == "create_file":
            path = c["path"]
            content = c.get("content","")
            print(f"→ create_file {path}")
            os.makedirs(os.path.dirname(path) or ".", exist_ok=True)
            with open(path, "w", encoding="utf-8") as fp:
                fp.write(content)
        elif t == "replace_file":
            path = c["path"]
            content = c.get("content","")
            print(f"→ replace_file {path}")
            with open(path, "w", encoding="utf-8") as fp:
                fp.write(content)
        elif t == "insert_line":
            path = c["path"]
            line = c["line"] - 1
            content = c["content"]
            print(f"→ insert_line {path}@{c['line']}")
            lines = open(path, encoding="utf-8").read().splitlines()
            lines.insert(line, content)
            open(path, "w", encoding="utf-8").write("\n".join(lines))
        elif t == "edit_line":
            path = c["path"]
            line = c["line"] - 1
            content = c["content"]
            print(f"→ edit_line {path}@{c['line']}")
            lines = open(path, encoding="utf-8").read().splitlines()
            lines[line] = content
            open(path, "w", encoding="utf-8").write("\n".join(lines))
        elif t == "append":
            path = c["path"]
            content = c["content"]
            print(f"→ append {path}")
            with open(path, "a", encoding="utf-8") as fp:
                fp.write(content)
        elif t == "delete":
            path = c["path"]
            print(f"→ delete {path}")
            if os.path.isdir(path):
                os.rmdir(path)
            else:
                os.remove(path)
        elif t == "update_directory":
            root = c.get("root", ".")
            out  = c.get("output", "directory.txt")
            write_directory(root, out)
        else:
            print(f"[WARN] unknown command type: {t}")
    print("[+] All commands executed.")

def main():
    os.makedirs(OUTPUT_ROOT, exist_ok=True)
    print(f"→ entering {OUTPUT_ROOT}/")
    os.chdir(OUTPUT_ROOT)

    try:
        txt = pyperclip.paste()
        data = json.loads(txt)
    except Exception as e:
        print("[-] Failed to read/parse JSON from clipboard:", e)
        sys.exit(1)

    cmds = data.get("commands")
    if not isinstance(cmds, list):
        print("[-] Invalid JSON: missing 'commands' array.")
        sys.exit(1)

    run_commands(cmds)

if __name__ == "__main__":
    main()
