# Quick Start Guide: Viewing UML Diagrams

## 🚀 Fastest Ways to View the Diagrams

### Option 1: GitHub (Mermaid Diagrams Only) ⭐ EASIEST
Simply view these files directly on GitHub:
- `use_case_diagram_mermaid.md` 
- `activity_diagrams_mermaid.md`

GitHub automatically renders Mermaid diagrams in markdown files!

### Option 2: PNG Images (All Diagrams) ⭐ INSTANT VIEW
Simply click on any PNG file in the diagrams directory:
- `use_case_diagram.png`
- `activity_authentication.png`
- `activity_price_comparison.png`
- `activity_price_forecasting.png`
- `activity_data_collection.png`
- `activity_mobile_search.png`
- `activity_ml_training.png`

Perfect for quick viewing in GitHub or any file browser!

### Option 3: Online PlantUML Viewer (All Diagrams) ⭐ NO INSTALLATION
1. Go to: http://www.plantuml.com/plantuml/uml/
2. Copy the content from any `.puml` file
3. Paste it in the text box
4. The diagram renders automatically!

### Option 4: Mermaid Live Editor (Mermaid Diagrams)
1. Go to: https://mermaid.live/
2. Copy content from `use_case_diagram_mermaid.md` or `activity_diagrams_mermaid.md`
3. Paste the mermaid code (without the markdown code fence)
4. View and export!

### Option 5: VS Code Extension (For Developers)
**For PlantUML:**
1. Install "PlantUML" extension by jebbs
2. Open any `.puml` file
3. Press `Alt+D` or right-click → "Preview Current Diagram"

**For Mermaid:**
1. Install "Markdown Preview Mermaid Support" extension
2. Open any `.md` file with mermaid diagrams
3. Press `Ctrl+Shift+V` for markdown preview

### Option 6: IntelliJ IDEA / PyCharm (For Developers)
1. Install "PlantUML integration" plugin
2. Right-click on `.puml` file → "Show PlantUML Preview"

---

## 📥 Generating Image Files

### Using PlantUML CLI (Linux/Mac)

Install PlantUML:
```bash
# Ubuntu/Debian
sudo apt-get install plantuml

# macOS
brew install plantuml
```

Generate images:
```bash
# Generate all diagrams as PNG
cd diagrams/
plantuml *.puml

# Generate as SVG (better quality)
plantuml -tsvg *.puml

# Generate as PDF
plantuml -tpdf *.puml
```

### Using Docker (Cross-platform)
```bash
# From repository root
docker run --rm -v $(pwd)/diagrams:/data plantuml/plantuml *.puml
```

### Using Online Service (Automated)
PlantUML Proxy URL format:
```
http://www.plantuml.com/plantuml/png/[encoded_diagram]
```

---

## 📋 Diagram Files Quick Reference

### Use Case Diagram
- **PlantUML**: `use_case_diagram.puml` (detailed, professional)
- **Mermaid**: `use_case_diagram_mermaid.md` (GitHub-friendly)

### Activity Diagrams
1. **Authentication Flow**
   - PlantUML: `activity_authentication.puml`
   - Mermaid: see `activity_diagrams_mermaid.md` section 1

2. **Price Comparison Workflow**
   - PlantUML: `activity_price_comparison.puml`
   - Mermaid: see `activity_diagrams_mermaid.md` section 2

3. **Price Forecasting Workflow**
   - PlantUML: `activity_price_forecasting.puml`
   - Mermaid: see `activity_diagrams_mermaid.md` section 3

4. **Data Collection Process**
   - PlantUML: `activity_data_collection.puml`
   - Mermaid: see `activity_diagrams_mermaid.md` section 4

5. **Mobile App Search**
   - PlantUML: `activity_mobile_search.puml`
   - Mermaid: see `activity_diagrams_mermaid.md` section 5

6. **ML Model Training**
   - PlantUML: `activity_ml_training.puml`
   - Mermaid: see `activity_diagrams_mermaid.md` section 6

---

## 🎨 PlantUML vs Mermaid: Which to Use?

### Use PlantUML When:
- ✅ You need professional-quality diagrams for documentation
- ✅ Complex swimlane diagrams with detailed error handling
- ✅ Generating PDFs or high-resolution images
- ✅ Using professional diagramming tools (IntelliJ, VS Code)
- ✅ Academic or professional presentations

### Use Mermaid When:
- ✅ Viewing directly on GitHub
- ✅ Quick reference in markdown documentation
- ✅ Simple web-based viewing
- ✅ No installation required
- ✅ Embedded in README files

**Both formats are provided for maximum accessibility!**

---

## 🔧 Troubleshooting

### PlantUML: "Graphviz is not installed"
```bash
# Ubuntu/Debian
sudo apt-get install graphviz

# macOS
brew install graphviz

# Or use PlantUML online viewer (no installation needed)
```

### VS Code: "Preview not showing"
- Ensure the PlantUML extension is installed and enabled
- Check that Java is installed: `java -version`
- Try the online viewer as an alternative

### Mermaid: "Diagram not rendering"
- Ensure you're using a compatible viewer
- Check that the mermaid code blocks are properly formatted
- Try the Mermaid Live Editor for testing

---

## 📚 Resources

### PlantUML
- Official Website: https://plantuml.com/
- Language Reference: https://plantuml.com/guide
- Online Server: http://www.plantuml.com/plantuml/

### Mermaid
- Official Website: https://mermaid.js.org/
- Live Editor: https://mermaid.live/
- Documentation: https://mermaid.js.org/intro/

### UML Standards
- Use Case Diagrams: https://www.uml-diagrams.org/use-case-diagrams.html
- Activity Diagrams: https://www.uml-diagrams.org/activity-diagrams.html

---

## 💡 Tips for Team Members

1. **For Quick Reviews**: Use the Mermaid files on GitHub
2. **For Presentations**: Generate high-res images from PlantUML
3. **For Development**: Use VS Code or IntelliJ plugins
4. **For Documentation**: Embed generated images in reports

---

## ✅ Verification Checklist

Before presenting or submitting:
- [ ] All `.puml` files have matching `@startuml` and `@enduml` tags
- [ ] Diagrams render without errors in online viewer
- [ ] Generated images are clear and readable
- [ ] Mermaid diagrams display correctly on GitHub
- [ ] All use cases from requirements are included
- [ ] Activity diagrams cover main system workflows

---

Last Updated: February 16, 2026
