# import geojson
# file = open('innopolis_roads.geojson', 'r')
# data = dict(geojson.loads(file.read()))
# for i in data.keys():
#     print(i)
# print()
#
#
# print()
# print(data.keys())
# print()
# print(data['type'])
# print()
# print(data['crs'])
# print()
# for i in range(len(data['features'])):
#     print(data['features'][i]['geometry'])









from PIL import Image


im_1 = Image.open('innopolis.png', 'r')
im_2 = Image.open('road_innopolis_2.png', 'r')
pixels_1 = im_1.load()
x_1, y_1 = im_1.size

pixels_2 = im_2.load()
x_2, y_2 = im_2.size

matrix_road = []
matrix_road_bool = []
matrix_road_weight = []

###################################################################################################################################################

for i in range(1, x_2, 1):

    a = []
    b = []
    c = []

    for j in range(1, y_2, 1):

        pixel_2 = pixels_2[i, j]

        if pixel_2[0] < 100 and pixel_2[1] < 100 and pixel_2[2] < 100:
            a.append(1)
            c.append(0)
        else:
            a.append(0)
            c.append(1)

        b.append(0)

    matrix_road_weight.append(b)
    matrix_road_bool.append(c)
    matrix_road.append(a)



################################################################################################################################################################################################################

def rec(x, y):

    global matrix_road_bool, matrix_road_weight

    if x < len(matrix_road_bool) and y < len(matrix_road_bool[x]) and x < x_1 and y < x_2 and matrix_road_bool[x][y] != 1:
        matrix_road_bool[x][y] = 1
        r, g, b = pixels_1[x, y]

        if matrix_road and r > 220 and g > 220 and b > 220:
            matrix_road_weight[x][y] = 3 * 5

        rec(x + 1, y)
        rec(x, y + 1)


###################################################################################################################################################################################################################################################################################################


for i in range(1, x_1, 1):
    for j in range(1, y_2, 1):
        rec(i, j)

########################################################################################################################################################################################################################################################################################

# f = False
# for i in matrix_road_weight:
#     for j in i:
#         print(j, end='')
#         if j != 0:
#             f = True
#     print()


for i in range(1, min(len(matrix_road_weight), x_1), 1):
    for j in range(1, min(len(matrix_road_weight[i]), y_1), 1):

        if(matrix_road_weight[i][j] == 0):
            r = 255
            g = 255
            b = 255
        else:
            r = 150
            g = 150
            b = 150
        pixels_1[i, j] = r, g, b

im_1.save("new_map_1.jpg")
######################################################################################################################################
field = []
for i in range(len(matrix_road_weight) // 2 + 1):
    a = []
    for j in range(len(matrix_road_weight[i]) // 2 + 1):
        a.append(0)
    field.append(a)

for i in range(1, len(matrix_road_weight), 1):
    for j in range(1, len(matrix_road_weight[i]), 1):
        field[i // 2][ j // 2] += matrix_road_weight[i][j]
#######################################################################################################################################
for i in field:
    for j in i:
        print(j, end='\t')
    print()

color = (0, 0, 120)
im_3 = Image.new('RGB', (len(field), len(field[0])), color)
pixels_3 = im_3.load()
for i in range(0, len(field), 1):
    for j in range(0, len(field[i]), 1):

        if int(field[i][j]) > 10:
            r = 100 - int(field[i][j]) * 2
            g = 100 + int(field[i][j]) * 2
            b = 100 + int(field[i][j]) * 3
            print(int(field[i][j]))
        else:
            r = 255
            g = 255
            b = 255

        pixels_3[i, j] = int(r), int(g), int(b)

im_3.save("new_map_2.jpg")


im_4 = Image.open('innopolis.png', 'r')
im_5 = Image.open('new_map_1.jpg', 'r')



pixels_4 = im_4.load()
x_4, y_4 = im_4.size


pixels_5 = im_5.load()
x_5, y_5 = im_5.size


for i in range(min(x_4, x_5)):
    for j in range(min(y_5, y_5)):

        pixel_4 = pixels_4[i, j]
        pixel_5 = pixels_5[i, j]

        r1 = pixel_4[0]
        g1 = pixel_4[1]
        b1 = pixel_4[2]

        r2 = pixel_5[0]
        g2 = pixel_5[1]
        b2 = pixel_5[2]

        r = int(r1)
        g = int(g1)
        b = int(b1)
        if(r2 < 200):
            r = 35
            g = 113
            b = 230


        pixels_4[i, j] = r, g, b


im_4.save("finish_map.jpg")
