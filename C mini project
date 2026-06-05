#include <stdio.h>
#include <stdlib.h>
#include <math.h>

#define HEIGHT 20
#define WIDTH 60
#define MAX_SHAPES 100

// ==========================================
// DATA STRUCTURES & DEFINITIONS
// ==========================================

typedef enum {
    SHAPE_LINE,
    SHAPE_RECTANGLE,
    SHAPE_CIRCLE,
    SHAPE_TRIANGLE
} ShapeType;

typedef struct {
    int x1, y1;
    int x2, y2;
} LineData;

typedef struct {
    int x, y;
    int w, h;
} RectData;

typedef struct {
    int cx, cy;
    int r;
} CircleData;

typedef struct {
    int x1, y1;
    int x2, y2;
    int x3, y3;
} TriangleData;

typedef struct {
    int id;
    ShapeType type;
    union {
        LineData line;
        RectData rect;
        CircleData circle;
        TriangleData triangle;
    } data;
} Shape;

// ==========================================
// FUNCTION PROTOTYPES
// ==========================================

void init_canvas(char canvas[HEIGHT][WIDTH]);
void display_canvas(char canvas[HEIGHT][WIDTH]);
int add_line(Shape shapes[], int *count, int *next_id, int x1, int y1, int x2, int y2);
int add_rect(Shape shapes[], int *count, int *next_id, int x, int y, int w, int h);
int add_circle(Shape shapes[], int *count, int *next_id, int cx, int cy, int r);
int add_triangle(Shape shapes[], int *count, int *next_id, int x1, int y1, int x2, int y2, int x3, int y3);
int delete_shape(Shape shapes[], int *count, int id);
void render_shapes(char canvas[HEIGHT][WIDTH], Shape shapes[], int count);

// ==========================================
// GRAPHICS IMPLEMENTATION
// ==========================================

/* Initialize the canvas with space characters */
void init_canvas(char canvas[HEIGHT][WIDTH]) {
    for (int i = 0; i < HEIGHT; i++) {
        for (int j = 0; j < WIDTH; j++) {
            canvas[i][j] = ' ';
        }
    }
}

/* Display the canvas in the console with a border and coordinate labels */
void display_canvas(char canvas[HEIGHT][WIDTH]) {
    // Print column coordinate headers (tens and units) for alignment help
    printf("     ");
    for (int j = 0; j < WIDTH; j++) {
        if (j % 10 == 0) {
            printf("%d", j / 10);
        } else {
            printf(" ");
        }
    }
    printf("\n     ");
    for (int j = 0; j < WIDTH; j++) {
        printf("%d", j % 10);
    }
    printf("\n");

    // Print top border of canvas
    printf("    +");
    for (int j = 0; j < WIDTH; j++) {
        printf("-");
    }
    printf("+\n");

    // Print canvas rows with row labels and borders
    for (int i = 0; i < HEIGHT; i++) {
        printf("%3d |", i);
        for (int j = 0; j < WIDTH; j++) {
            printf("%c", canvas[i][j]);
        }
        printf("|\n");
    }

    // Print bottom border of canvas
    printf("    +");
    for (int j = 0; j < WIDTH; j++) {
        printf("-");
    }
    printf("+\n");
}

/* Active shapes management functions */

int add_line(Shape shapes[], int *count, int *next_id, int x1, int y1, int x2, int y2) {
    if (*count >= MAX_SHAPES) {
        return -1; // Canvas shapes list is full
    }
    shapes[*count].id = (*next_id)++;
    shapes[*count].type = SHAPE_LINE;
    shapes[*count].data.line.x1 = x1;
    shapes[*count].data.line.y1 = y1;
    shapes[*count].data.line.x2 = x2;
    shapes[*count].data.line.y2 = y2;
    (*count)++;
    return shapes[*count - 1].id;
}

int add_rect(Shape shapes[], int *count, int *next_id, int x, int y, int w, int h) {
    if (*count >= MAX_SHAPES) {
        return -1;
    }
    shapes[*count].id = (*next_id)++;
    shapes[*count].type = SHAPE_RECTANGLE;
    shapes[*count].data.rect.x = x;
    shapes[*count].data.rect.y = y;
    shapes[*count].data.rect.w = w;
    shapes[*count].data.rect.h = h;
    (*count)++;
    return shapes[*count - 1].id;
}

int add_circle(Shape shapes[], int *count, int *next_id, int cx, int cy, int r) {
    if (*count >= MAX_SHAPES) {
        return -1;
    }
    shapes[*count].id = (*next_id)++;
    shapes[*count].type = SHAPE_CIRCLE;
    shapes[*count].data.circle.cx = cx;
    shapes[*count].data.circle.cy = cy;
    shapes[*count].data.circle.r = r;
    (*count)++;
    return shapes[*count - 1].id;
}

int add_triangle(Shape shapes[], int *count, int *next_id, int x1, int y1, int x2, int y2, int x3, int y3) {
    if (*count >= MAX_SHAPES) {
        return -1;
    }
    shapes[*count].id = (*next_id)++;
    shapes[*count].type = SHAPE_TRIANGLE;
    shapes[*count].data.triangle.x1 = x1;
    shapes[*count].data.triangle.y1 = y1;
    shapes[*count].data.triangle.x2 = x2;
    shapes[*count].data.triangle.y2 = y2;
    shapes[*count].data.triangle.x3 = x3;
    shapes[*count].data.triangle.y3 = y3;
    (*count)++;
    return shapes[*count - 1].id;
}

int delete_shape(Shape shapes[], int *count, int id) {
    int index = -1;
    for (int i = 0; i < *count; i++) {
        if (shapes[i].id == id) {
            index = i;
            break;
        }
    }
    if (index == -1) {
        return 0; // Shape not found
    }
    // Shift elements left to remove the shape from list
    for (int i = index; i < *count - 1; i++) {
        shapes[i] = shapes[i + 1];
    }
    (*count)--;
    return 1; // Successfully deleted
}

/* Bresenham's Line Algorithm */
static void render_line(char canvas[HEIGHT][WIDTH], int x1, int y1, int x2, int y2) {
    int dx = abs(x2 - x1);
    int dy = abs(y2 - y1);
    int sx = (x1 < x2) ? 1 : -1;
    int sy = (y1 < y2) ? 1 : -1;
    int err = dx - dy;

    while (1) {
        if (x1 >= 0 && x1 < WIDTH && y1 >= 0 && y1 < HEIGHT) {
            // Draw horizontal lines using '_' and other lines using '*'
            char draw_char = (dy == 0) ? '_' : '*';
            canvas[y1][x1] = draw_char;
        }
        if (x1 == x2 && y1 == y2) {
            break;
        }
        int e2 = 2 * err;
        if (e2 > -dy) {
            err -= dy;
            x1 += sx;
        }
        if (e2 < dx) {
            err += dx;
            y1 += sy;
        }
    }
}

/* Rectangle Rendering */
static void render_rect(char canvas[HEIGHT][WIDTH], int x, int y, int w, int h) {
    // Draw top and bottom horizontal borders with '_'
    for (int i = 0; i < w; i++) {
        int tx = x + i;
        if (tx >= 0 && tx < WIDTH) {
            // Top border
            if (y >= 0 && y < HEIGHT) {
                canvas[y][tx] = '_';
            }
            // Bottom border
            int by = y + h - 1;
            if (by >= 0 && by < HEIGHT && by != y) {
                canvas[by][tx] = '_';
            }
        }
    }

    // Draw left and right vertical borders with '*'
    for (int j = 0; j < h; j++) {
        int ty = y + j;
        if (ty >= 0 && ty < HEIGHT) {
            // Left border
            if (x >= 0 && x < WIDTH) {
                canvas[ty][x] = '*';
            }
            // Right border
            int rx = x + w - 1;
            if (rx >= 0 && rx < WIDTH && rx != x) {
                canvas[ty][rx] = '*';
            }
        }
    }
}

/* Aspect-ratio compensated Circle Rendering */
static void render_circle(char canvas[HEIGHT][WIDTH], int cx, int cy, int r) {
    if (r <= 0) {
        if (cx >= 0 && cx < WIDTH && cy >= 0 && cy < HEIGHT) {
            canvas[cy][cx] = '*';
        }
        return;
    }

    // Characters in terminal are taller than they are wide (ratio ~ 1.8:1).
    // Compensate for this by scaling the X coordinates.
    const double ASPECT_RATIO = 1.8;

    for (int dy = -r; dy <= r; dy++) {
        int y = cy + dy;
        if (y < 0 || y >= HEIGHT) {
            continue;
        }

        // Calculate offset on x-axis based on circle equation: x^2 + y^2 = r^2
        double dx = ASPECT_RATIO * sqrt(r * r - dy * dy);

        int x1 = (int)round(cx - dx);
        int x2 = (int)round(cx + dx);

        if (x1 >= 0 && x1 < WIDTH) {
            canvas[y][x1] = (dy == -r || dy == r) ? '_' : '*';
        }
        if (x2 >= 0 && x2 < WIDTH) {
            canvas[y][x2] = (dy == -r || dy == r) ? '_' : '*';
        }
    }
}

/* Triangle Rendering */
static void render_triangle(char canvas[HEIGHT][WIDTH], int x1, int y1, int x2, int y2, int x3, int y3) {
    render_line(canvas, x1, y1, x2, y2);
    render_line(canvas, x2, y2, x3, y3);
    render_line(canvas, x3, y3, x1, y1);
}

/* Render all shapes onto the canvas buffer */
void render_shapes(char canvas[HEIGHT][WIDTH], Shape shapes[], int count) {
    for (int i = 0; i < count; i++) {
        switch (shapes[i].type) {
            case SHAPE_LINE:
                render_line(canvas, 
                            shapes[i].data.line.x1, shapes[i].data.line.y1,
                            shapes[i].data.line.x2, shapes[i].data.line.y2);
                break;
            case SHAPE_RECTANGLE:
                render_rect(canvas, 
                            shapes[i].data.rect.x, shapes[i].data.rect.y,
                            shapes[i].data.rect.w, shapes[i].data.rect.h);
                break;
            case SHAPE_CIRCLE:
                render_circle(canvas, 
                              shapes[i].data.circle.cx, shapes[i].data.circle.cy,
                              shapes[i].data.circle.r);
                break;
            case SHAPE_TRIANGLE:
                render_triangle(canvas, 
                                shapes[i].data.triangle.x1, shapes[i].data.triangle.y1,
                                shapes[i].data.triangle.x2, shapes[i].data.triangle.y2,
                                shapes[i].data.triangle.x3, shapes[i].data.triangle.y3);
                break;
        }
    }
}

// ==========================================
// USER INTERFACE & MAIN LOOP
// ==========================================

/* Helper to clear the terminal screen */
void clear_screen() {
#ifdef _WIN32
    system("cls");
#else
    system("clear");
#endif
}

/* Robust integer input retrieval function to handle typos and check boundaries */
int get_int_input(const char *prompt, int min_val, int max_val) {
    int val;
    char term;
    while (1) {
        printf("%s", prompt);
        if (scanf("%d%c", &val, &term) != 2 || term != '\n') {
            printf("Error: Invalid input format. Please enter an integer.\n");
            // Clear input buffer
            int c;
            while ((c = getchar()) != '\n' && c != EOF);
        } else if (val < min_val || val > max_val) {
            printf("Error: Value out of range (%d to %d). Please try again.\n", min_val, max_val);
        } else {
            return val;
        }
    }
}

/* Helper to press Enter to continue */
void press_enter_to_continue() {
    printf("\nPress Enter to return to the menu...");
    int c;
    while ((c = getchar()) != '\n' && c != EOF); // Flush key buffer
    getchar(); // Wait for actual keypress
}

/* List active shapes with their structural details */
void list_shapes(Shape shapes[], int count) {
    if (count == 0) {
        printf("No objects currently on the canvas.\n");
        return;
    }
    printf("Objects list:\n");
    for (int i = 0; i < count; i++) {
        printf("  [ID %2d] ", shapes[i].id);
        switch (shapes[i].type) {
            case SHAPE_LINE:
                printf("Line from (%d, %d) to (%d, %d)\n", 
                       shapes[i].data.line.x1, shapes[i].data.line.y1,
                       shapes[i].data.line.x2, shapes[i].data.line.y2);
                break;
            case SHAPE_RECTANGLE:
                printf("Rectangle at (%d, %d), size %dx%d\n", 
                       shapes[i].data.rect.x, shapes[i].data.rect.y,
                       shapes[i].data.rect.w, shapes[i].data.rect.h);
                break;
            case SHAPE_CIRCLE:
                printf("Circle centered at (%d, %d), radius %d\n", 
                       shapes[i].data.circle.cx, shapes[i].data.circle.cy,
                       shapes[i].data.circle.r);
                break;
            case SHAPE_TRIANGLE:
                printf("Triangle vertices: (%d, %d), (%d, %d), (%d, %d)\n", 
                       shapes[i].data.triangle.x1, shapes[i].data.triangle.y1,
                       shapes[i].data.triangle.x2, shapes[i].data.triangle.y2,
                       shapes[i].data.triangle.x3, shapes[i].data.triangle.y3);
                break;
        }
    }
}

int main() {
    char canvas[HEIGHT][WIDTH];
    Shape shapes[MAX_SHAPES];
    int shape_count = 0;
    int next_id = 1;
    int choice = 0;

    while (1) {
        // Prepare canvas and render all active shapes
        init_canvas(canvas);
        render_shapes(canvas, shapes, shape_count);

        // Display interface
        clear_screen();
        printf("==================== 2D GRAPHICS EDITOR ====================\n");
        display_canvas(canvas);
        printf("============================================================\n");
        list_shapes(shapes, shape_count);
        printf("============================================================\n");
        printf("Menu:\n");
        printf("  1. Add a Line (uses * and _)\n");
        printf("  2. Add a Rectangle (uses * and _)\n");
        printf("  3. Add a Circle\n");
        printf("  4. Add a Triangle\n");
        printf("  5. Delete an Object by ID\n");
        printf("  6. Clear all Objects\n");
        printf("  7. Exit\n");
        printf("============================================================\n");

        choice = get_int_input("Enter choice (1-7): ", 1, 7);

        if (choice == 7) {
            printf("\nExiting program. Goodbye!\n");
            break;
        }

        switch (choice) {
            case 1: { // Add a Line
                printf("\n--- Add a Line ---\n");
                int x1 = get_int_input("Enter starting X (0-59): ", 0, WIDTH - 1);
                int y1 = get_int_input("Enter starting Y (0-19): ", 0, HEIGHT - 1);
                int x2 = get_int_input("Enter ending X (0-59): ", 0, WIDTH - 1);
                int y2 = get_int_input("Enter ending Y (0-19): ", 0, HEIGHT - 1);
                
                int new_id = add_line(shapes, &shape_count, &next_id, x1, y1, x2, y2);
                if (new_id == -1) {
                    printf("\nError: Maximum shapes count reached! Delete some objects first.\n");
                    press_enter_to_continue();
                }
                break;
            }
            case 2: { // Add a Rectangle
                printf("\n--- Add a Rectangle ---\n");
                int x = get_int_input("Enter top-left corner X (0-59): ", 0, WIDTH - 1);
                int y = get_int_input("Enter top-left corner Y (0-19): ", 0, HEIGHT - 1);
                
                int max_w = WIDTH - x;
                int max_h = HEIGHT - y;
                
                int w = get_int_input("Enter width (1 to max available): ", 1, max_w);
                int h = get_int_input("Enter height (1 to max available): ", 1, max_h);

                int new_id = add_rect(shapes, &shape_count, &next_id, x, y, w, h);
                if (new_id == -1) {
                    printf("\nError: Maximum shapes count reached! Delete some objects first.\n");
                    press_enter_to_continue();
                }
                break;
            }
            case 3: { // Add a Circle
                printf("\n--- Add a Circle ---\n");
                int cx = get_int_input("Enter center X (0-59): ", 0, WIDTH - 1);
                int cy = get_int_input("Enter center Y (0-19): ", 0, HEIGHT - 1);
                int r = get_int_input("Enter radius (1-30): ", 1, 30);

                int new_id = add_circle(shapes, &shape_count, &next_id, cx, cy, r);
                if (new_id == -1) {
                    printf("\nError: Maximum shapes count reached! Delete some objects first.\n");
                    press_enter_to_continue();
                }
                break;
            }
            case 4: { // Add a Triangle
                printf("\n--- Add a Triangle ---\n");
                int x1 = get_int_input("Enter Vertex 1 X (0-59): ", 0, WIDTH - 1);
                int y1 = get_int_input("Enter Vertex 1 Y (0-19): ", 0, HEIGHT - 1);
                int x2 = get_int_input("Enter Vertex 2 X (0-59): ", 0, WIDTH - 1);
                int y2 = get_int_input("Enter Vertex 2 Y (0-19): ", 0, HEIGHT - 1);
                int x3 = get_int_input("Enter Vertex 3 X (0-59): ", 0, WIDTH - 1);
                int y3 = get_int_input("Enter Vertex 3 Y (0-19): ", 0, HEIGHT - 1);

                int new_id = add_triangle(shapes, &shape_count, &next_id, x1, y1, x2, y2, x3, y3);
                if (new_id == -1) {
                    printf("\nError: Maximum shapes count reached! Delete some objects first.\n");
                    press_enter_to_continue();
                }
                break;
            }
            case 5: { // Delete an Object
                printf("\n--- Delete an Object ---\n");
                if (shape_count == 0) {
                    printf("No objects to delete.\n");
                    press_enter_to_continue();
                    break;
                }
                list_shapes(shapes, shape_count);
                int target_id = get_int_input("Enter the ID of the object to delete: ", 1, next_id - 1);
                
                if (delete_shape(shapes, &shape_count, target_id)) {
                    printf("Object with ID %d successfully deleted!\n", target_id);
                } else {
                    printf("Error: Object with ID %d not found.\n", target_id);
                    press_enter_to_continue();
                }
                break;
            }
            case 6: { // Clear all Objects
                shape_count = 0;
                printf("\nCanvas cleared successfully!\n");
                break;
            }
        }
    }

    return 0;
}
