# ai-architect
"""
AI ARCHITECT & MATERIAL ESTIMATOR
Auto Layout Plan + Material Estimation Generator
"""

import math
import matplotlib.pyplot as plt
import matplotlib.patches as patches

class AIArchitect:
    def __init__(self, length, width, height=8.5, structure_type="Porta Cabin"):
        self.length = float(length)
        self.width = float(width)
        self.height = float(height)
        self.structure_type = structure_type

    def estimate_materials(self):
        floor_area = self.length * self.width
        perimeter = 2 * (self.length + self.width)
        wall_area = perimeter * self.height
        roof_area = floor_area

        # 1. 8x4 Feet Panels / Sheets (Cement Board / GI Sheet) -> 32 sq.ft per sheet
        wall_sheets = math.ceil(wall_area / 32)
        roof_sheets = math.ceil(roof_area / 32)
        floor_sheets = math.ceil(floor_area / 32)
        total_sheets = wall_sheets + roof_sheets + floor_sheets

        # 2. Steel Framing / Structure (Columns & Rafters)
        # Running feet calculation for 2-foot grid spacing
        vertical_studs = (math.ceil(self.length / 2) + 1) * 2 + (math.ceil(self.width / 2) + 1) * 2
        vertical_running_ft = vertical_studs * self.height
        base_top_tracks_ft = perimeter * 2
        total_steel_running_ft = round(vertical_running_ft + base_top_tracks_ft, 2)

        # 3. Insulation (Thermocol / Rockwool)
        insulation_sqft = round(wall_area + roof_area, 2)

        # 4. Fasteners & Screws
        screws_count = total_sheets * 35

        # 5. Joint Compound / Body Filler
        filler_kg = math.ceil(total_sheets * 0.4)

        return {
            "Dimension": f"{self.length} ft x {self.width} ft x {self.height} ft",
            "Floor Area": f"{floor_area:.2f} sq.ft",
            "Wall Area": f"{wall_area:.2f} sq.ft",
            "Framing Running Feet (Approx)": f"{total_steel_running_ft} R.Ft",
            "8x4 Sheets (Wall/Roof/Floor)": f"{total_sheets} nos",
            "Insulation Area": f"{insulation_sqft} sq.ft",
            "Self-Drilling Screws": f"{screws_count} pcs",
            "Joint Compound / Filler": f"{filler_kg} kg"
        }

    def draw_layout(self, output_filename="naksha.png"):
        fig, ax = plt.subplots(figsize=(8, 6))

        # Main Outer Boundary
        outer_box = patches.Rectangle((0, 0), self.length, self.width, 
                                      linewidth=3, edgecolor='#2c3e50', facecolor='#ecf0f1')
        ax.add_patch(outer_box)

        # Door representation (Bottom wall par 3ft door)
        door_width = min(3.0, self.length * 0.3)
        door = patches.Rectangle((self.length * 0.1, 0), door_width, 0.4, 
                                 linewidth=2, edgecolor='#e74c3c', facecolor='#ffffff')
        ax.add_patch(door)
        ax.text(self.length * 0.1 + (door_width / 2), 0.6, "DOOR", 
                ha='center', va='bottom', fontsize=8, color='#c0392b', weight='bold')

        # Window representation (Top wall par 4ft window)
        window_width = min(4.0, self.length * 0.4)
        window = patches.Rectangle((self.length * 0.5, self.width - 0.4), window_width, 0.4, 
                                   linewidth=2, edgecolor='#2980b9', facecolor='#ffffff')
        ax.add_patch(window)
        ax.text(self.length * 0.5 + (window_width / 2), self.width - 0.7, "WINDOW", 
                ha='center', va='top', fontsize=8, color='#2980b9', weight='bold')

        # Dimensions Text
        ax.text(self.length / 2, -1.2, f"Length: {self.length} ft", ha='center', fontsize=10, weight='bold')
        ax.text(-1.2, self.width / 2, f"Width: {self.width} ft", va='center', rotation='vertical', fontsize=10, weight='bold')

        # Center Room Info
        ax.text(self.length / 2, self.width / 2, f"{self.structure_type}\n({self.length} x {self.width} FT)\nArea: {self.length * self.width:.1f} sq.ft", 
                ha='center', va='center', fontsize=11, weight='bold', color='#34495e')

        # Plot Settings
        ax.set_xlim(-2, self.length + 2)
        ax.set_ylim(-2, self.width + 2)
        ax.set_aspect('equal')
        ax.axis('off')
        plt.title(f"AI Architect 2D Plan - {self.structure_type}", fontsize=13, weight='bold', pad=15)

        plt.savefig(output_filename, bbox_inches='tight', dpi=300)
        print(f"\n[+] Naksha safalta-purvak ban gaya: {output_filename}")


if __name__ == "__main__":
    print("========================================")
    print("      AI ARCHITECT & ESTIMATOR          ")
    print("========================================")

    # Example inputs (User badal sakta hai)
    cabin_length = 20.0  # Feet
    cabin_width = 10.0   # Feet
    cabin_height = 8.5   # Feet

    architect = AIArchitect(length=cabin_length, width=cabin_width, height=cabin_height, structure_type="Porta Cabin / Office")

    # 1. Material Calculation
    materials = architect.estimate_materials()
    print("\n--- MATERIAL ESTIMATION REPORT ---")
    for item, qty in materials.items():
        print(f"• {item}: {qty}")

    # 2. Draw Floor Plan
    architect.draw_layout("naksha.png")
