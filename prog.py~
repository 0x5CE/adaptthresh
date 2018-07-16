#
# Implementation of algorithms described in research paper:
#
# "Efficient Implementation of Local Adaptive Thresholding
# Techniques Using Integral Images" by Faisal Shafait, Daniel Keysers,
# and Thomas M. Breuel
#
# Takes image, threshold, w (window size), k as arguments
# and outputs images and compares performance of the algorithms
#
# Author: Muazzam Ali Kazmi
#
# License: public domain
#

from PIL import Image
from math import sqrt
from time import time
from numpy import *

#Converts to grayscale
#takes image as argument
#
def grayscale(img):
	width, height = img.size
	new = Image.new("RGB", (width, height), "white")
	pixels = new.load()
	for i in range(width):
		for j in range(height):
			pixel = img.getpixel((i, j))
			r =   pixel[0]
			g = pixel[1]
			b =  pixel[2]

			gray = (r+g+b)/3.0

			pixels[i, j] = (int(gray), int(gray), int(gray))
	return new



#global binarization
#takes image and threshold as arguments
#
def bin_global(img, thres):
	print("Global binarization...")
	print("Threshold: " + str(thres))

	timeA = time()

	width, height = img.size
	new = Image.new("RGB", (width, height), "white")
	pixels = new.load()
	for i in range(width):
		for j in range(height):
			pixel = img.getpixel((i, j))
			pixel = pixel[0]

			if pixel > thres:
				pixel = 255.0
			if pixel  <= thres:
				pixel = 0.0

			pixels[i, j] = (int(pixel), int(pixel), int(pixel))

	timeB = time()
	print("Time taken: " + str(timeB - timeA))

	
	return new


#local adaptive thresholding using Sauvola’s technique
#takes image, window (w), and k (in the range [0.2, 0.5]) as arguments
#
def bin_sauvola(img, w, k):

	print("Sauvola’s technique...")
	print("w: " + str(w))
	print("k: " + str(k))

	timeA = time()


	width, height = img.size
	new = Image.new("RGB", (width, height), "white")
	pixels = new.load()
	for i in range(width):
		for j in range(height):
			pixel = img.getpixel((i, j))
			pixel = pixel[0]	#as all pixels are the same


			i_temp = i - (w/2)
			j_temp = j - (w/2)

			if i_temp < 0:
				i_temp = 0
			if j_temp < 0:
				j_temp = 0

			mean = 0.0
			total = 0
			for x in range(w):
				if (i_temp+x >= width):
					continue

				for y in range(w):
					if (j_temp+y >= height):
						continue

					pixel_temp = img.getpixel((i_temp+x, j_temp+y))
					pixel_temp = pixel_temp[0]
					mean += pixel_temp
					total += 1

			mean = mean / total

			sigma = 0.0
			total = 0
			for x in range(w):
				if (i_temp+x >= width):
						continue
				for y in range(w):
					if (j_temp+y >= height):
						continue
					pixel_temp = img.getpixel((i_temp+x, j_temp+y))
					pixel_temp = pixel_temp[0]
					sigma += (mean - pixel_temp) ** 2
					total += 1


			sigma = sqrt(sigma/total)

			#dymanically calculated threshold based on contrast of surrounding area
			thres = mean * (1.0 + k * ((sigma/128.0) - 1.0))

			if pixel > thres:
				pixel = 255.0
			if pixel  <= thres:
				pixel = 0.0

			pixels[i, j] = (int(pixel), int(pixel), int(pixel))

	timeB = time()
	print("Time taken: " + str(timeB - timeA))
	return new



#local adaptive thresholding using new technique prestented in the above-mentioned paper
#takes image, window (w), and k (in the range [0.2, 0.5]) as arguments
#
def bin_new(img, w, k):

	print("New technique...")
	print("w: " + str(w))
	print("k: " + str(k))

	timeA = time()


	width, height = img.size
	new = Image.new("RGB", (width, height), "white")
	pixels = new.load()

	I = zeros(shape=(width,height))
	I2 = zeros(shape=(width,height))

	for i in range(width):
		for j in range(height):
			pixel = img.getpixel((i, j))
			pixel = pixel[0]	#as all pixels are the same
			
			if i>0 and j>0:
				I[i][j] = I[i-1][j] - I[i-1][j-1] + I[i][j-1] + pixel
				I2[i][j] = I2[i-1][j] - I2[i-1][j-1] + I2[i][j-1] + pixel **2
			elif j>0:
				I[i][j] = I[i][j-1] + pixel
				I2[i][j] = I2[i][j-1] + pixel**2
			elif i>0:
				I[i][j] = I[i-1][j] + pixel
				I2[i][j] = I2[i-1][j] + pixel**2
			else:
				I[i][j] = pixel
				I2[i][j] = pixel**2

	for i in range(width):
		for j in range(height):
			pixel = img.getpixel((i, j))
			pixel = pixel[0]

			iplus = int(i+w/2)
			iminus = int(i-w/2)
			jplus = int(j+w/2)
			jminus = int(j-w/2)

			if iminus < 0:
				iminus = 0
			if jminus < 0:
				jminus = 0
			if iplus >= width:
				iplus = width-1
			if jplus >= height:
				jplus = height-1	

			mean = (I[iplus, jplus] + I[iminus, jminus] - I[iplus, jminus] - I[iminus, jplus]) / w**2
			sigma = I2[iplus, jplus] + I2[iminus, jminus] - I2[iplus, jminus] - I2[iminus, jplus]

			sigma = sqrt(sigma - (w**2*(mean**2))) / w**2


			#dymanically calculated threshold based on contrast of surrounding area
			thres = mean * (1.0 + k * ((sigma/128.0) - 1.0))

			if pixel > thres:
				pixel = 255.0
			if pixel  <= thres:
				pixel = 0.0


			pixels[i, j] = (int(pixel), int(pixel), int(pixel))

	timeB = time()
	print("Time taken: " + str(timeB - timeA))
	return new



def doimg(path, thres, w, k):
	img =  Image.open(path)
	imggray = grayscale(img)

	img2 = bin_global(imggray, thres)
	img3 = bin_sauvola(imggray, w, k)
	img4 = bin_new(imggray, w, k)

	print("Saving...")
	img2.save(path + "glb" + str(thres), 'png')
	img3.save(path + "svla" + str(w), 'png')
	img4.save(path + "new" + str(w), 'png')
