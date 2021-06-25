# Data-Encryption-Standard#include <math.h>
#include <stdlib.h>
#include <string.h>
#include <stdio.h>
int bit_get(const unsigned char *bits,int pos) {
	unsigned char mask ;
	int i ;
	mask=0x80;
	for(i=0;i<(pos%8 );i++)
    	mask = mask >> 1 ;
	//get the bit
       return(((mask&bits[(int) (pos/8)]) ==mask) ?  1 : 0) ; 

	}
 //bit set 
 void bit_set (unsigned char *bits,int pos,int state ) {
 	unsigned char mask ;
 	int i;
 	//aet a mask for the bit to set 
 	mask=0x80;
 	for(i=0;i<(pos%8);i++) {
	 
 	  mask=mask>>1;
 	 //set the bit
 	 if (state)
 	   bits[pos/8]= bits[pos/8]| mask;
 	 else
	  bits[pos / 8]=bits[pos/8] & (~mask)  ;
	 return;
	  
}

 //bit_rot_left
 void bit_rot_left(unsigned char *bits ,int size , int count) {
 	int   fbit,lbit,i,j;
 	
	 //rotate the buffer to the left 
	  
	  if (size>0){
	  	
	  	
	  		for(j=0;j<count;j++){
	  		
	  		for (i=0;i<=(size-1)/8;i++){
	  			
	  			//get the bit about to be shifted 
	  			lbit=bit_get(&bits[i],0);
	  			if(i==0){
	  				//save the bit shifted of the first byte for later
	  				fbit=lbit;
	  				
				  }
				else{
					
					bit_set(&bits[i-1],7,lbit);
					
				}
				
				//shift the current bytes to the left 
				bits[i]=bits[i]<<1;
			  }
			bit_set(bits,size-1,fbit) ; 
		  }
	  } 
	  return;              
 }
 
 
 //des,c
 unsigned char *ciph;
 static const int DesTransform[56]={
       57,49,41,33,25,17,9,1,58,50,42,34,26,18,
       10,2,59,51,43,35,27,19,11,3,60,52,44,36,
       63,55,47,39,31,23,15,7,62,54,46,38,30,22,
       14,6,61,53,45,37,29,21,13,5,28,20,12,4
 };
 
 //Define the number of rotation for computing skeys 
 static const int DesRotations[16]={
   1,1,2,2,2,2,2,2,1,2,2,2,2,2,2,1
 };
 
 //define a mapping for the permuted choice for subkeys 
 static const DesPermuted[48]={
 
    14,17,11,24,1,5,3,28,15,6,21,10,
    23,19,12,4,26,8,16,7,27,20,13,2,
    41,52,31,37,47,55,30,40,51,45,33,48,
    44,49,39,56,34,53,46,42,50,36,29,32
    
    
 };
 //Define a mapping for the initial permutation of data blpcks.
 
 static const int DesInitial[64]={
    58,50,42,34,26,18,10,2,60,52,44,36,28,20,12,4,
    62,54,46,38,30,22,14,6,64,56,48,40,32,24,16,8,
    57,49,41,33,25,17,9,1,59,51,43,35,27,19,11,3,
    61,53,45,37,29,21,13,5,63,55,47,39,31,23,15,7
    
 };
 //Define a mapping for the expansion permutation for data blocks 
 static const int DesExpansion[48]={
      32, 1 , 2 , 3 ,4,5,4,5,6,7,8,9,
      8,9,10,11,12,13,12,13,14,5,16,17,
      16,17,18,19,20,21,20,21,22,23,24,25,
      24,25,26,27,28,29,28,29,30,31,32,1
      
 
 };
 //deffine tables for the s-box substitutions permution for data blocks 
 static const int DesSbox[8] [4] [16]={
    {
    {14,4,13,1,2,15,11,8,3,10,6,12,5,9,0,7
    },
    {0,15,7,4,14,2,13,1,10,6,12,11,9,5,3,8
	},
	{4,1,14,8,13,6,2,11,15,12,9,7,3,10,5,0
	},
	{15,12,8,2,4,9,1,7,5,11,3,14,10,0,6,13
	},

	},
	
	
	{
	{15,1,8,14,6,11,3,4,9,7,2,13,12,0,5,10
	},
	{3,13,4,7,15,2,8,14,12,0,1,10,6,9,11,5
	},
	{0,14,7,11,10,4,13,1,5,8,12,6,9,3,2,15
	},
	{13,8,10,1,3,15,4,2,11,6,7,12,0,5,14,9
	},	
	},
	
	
	{
    {10,0,9,14,6,3,15,5,1,13,12,7,11,4,2,8
	},
	{13,7,0,9,2,4,6,10,2,8,5,14,12,11,15,1
	},
	{13,6,4,9,8,15,3,0,11,1,2,12,5,10,14,7
	},
	{1,10,13,0,6,9,8,7,4,15,14,3,11,5,2,12
	},
	},
	
	{
	{7,13,14,3,0,6,9,10,1,2,8,5,11,12,4,15
	},
	{13,8,11,5,6,15,0,3,4,7,2,12,1,10,14,9
	},
	{10,6,9,0,12,11,7,13,15,1,3,14,5,2,8,4
	},
	{3,15,0,6,10,1,13,8,9,4,5,11,12,7,2,14
	},
	
	},
	
	// 
	{
	{2,12,4,1,7,10,11,6,8,5,3,15,13,0,14,8
	},
	{14,11,2,12,4,7,13,1,5,0,15,10,3,9,8,6
	},
	{4,2,1,11,10,13,7,8,15,9,12,5,6,3,0,14
	},
	{11,0,12,7,1,14,2,13,6,15,0,9,10,4,5,3
	},
	},
	
	{
	{12,1,10,15,9,2,6,8,0,13,3,4,14,7,5,11
	},
	{10,15,4,2,7,12,9,5,6,1,13,14,0,11,3,8
	},
	{9,14,15,5,2,8,12,3,7,0,4,10,1,13,11,6
	},
	{4,3,2,12,9,5,15,10,11,14,1,7,6,0,8,13
	},
    },
    
    {

	{4,11,2,14,15,0,8,13,3,12,9,7,5,10,6,1
	},
	{13,0,11,7,4,9,1,10,14,3,5,12,2,15,8,6
	},
	{1,4,11,13,12,3,7,14,10,15,6,8,0,5,9,2
	},
	{6,11,13,8,1,4,10,7,9,5,0,15,14,2,3,12
	},
	},
	
	{
    {13,2,8,4,6,15,11,1,10,9,3,14,5,0,12,7
	},
	{1,15,13,8,10,3,7,4,12,5,6,11,0,14,9,2
	},
	{7,11,4,1,9,12,14,2,0,6,10,13,15,3,5,8
	},
	{2,1,14,7,4,10,8,13,15,12,9,0,3,5,6,11
	},
	},
		
};



//Deffine a mapping for the p-box permutation of data bloks 
static const int DesPbox[32]={
   16,7,20,21,29,12,28,17,1,15,25,26,5,18,31,10,
   2,8,24,14,32,27,3,9,19,13,30,6,22,11,4,25

};
//Definr a mappimg for the final permutation of data blocks 
static const int DesFinal[64]={
   40,8,48,16,56,24,64,32,39,7,47,15,55,23,63,31,
   38,6,46,14,54,22,62,30,37,5,45,13,53,21,61,29,
   36,4,44,12,52,20,60,28,35,3,43,11,51,19,59,27,
   34,2,42,10,50,18,58,26,33,1,41,9,49,17,57,25
};


//define a type for whether to encipher 0r decipher data 
typedef enum DesEorD_{encipher,decipher}
       DesEorD;
 void permute (unsigned char *bits,const int *mapping,int n) {
unsigned char temp[8];
int      i  ;
//permute the buffer using an-entry mapping .
memset(temp,0,(int)ceil(n/8));
for (i=0;i<n;i++)
   bit_set(temp,i,bit_get(bits,mapping[i]-1));
   memcpy(bits,temp,(int)ceil(n/8));
   return;
   
   }
   
int des_main(const unsigned char *source,unsigned char *target,const unsigned char *key,DesEorD direction){
static unsigned char subkeys[16] [7];
unsigned char    temp[8],lkey[4],rkey[4] ,lblk[6], rblk[6]
                     , fblk[6], xblk[6]	, sblk;
					 
int   row,col,i,j, k, p;
                     
			
//if key is null ,use the subkeys as computed in a previous call.
if(key!= NULL)	{
	//make a local copy of a key 
	
	memcpy(temp,key,8);
	//permitude and compress the key into 56 bits
	permute(temp,DesTransform,56);
	
	//splitt the key into two 28-bit blocks.
	memset(lkey,0,4);
	memset(rkey,0,4);
	for(j=0;j<28;j++)
	   bit_set(lkey,j,bet_get(temp,j));
	for(j=0;j<28;j++)
	   bit_set(rkey,j,bit_get(temp,j+28));
	   
	//compute the subkeys for each round .
	for(i=0;i<16;i++){
		
		//rotate each block according to its round .
		bit_rot_left(lkey,28,DesRotations[i]);
		bit_rot_left(rkey,28,DesRotations[i]);
		//concatenate the blocks into a single subkey.
		for(j=0;j<28;j++)
		   bit_set(subkeys[i],j,bit_get(lkey,j));
		for(j=0;j<28;j++)
		   bit_set(subkeys[i],j+28,bit_get(rkey,j));
		   
		   //do the permutation 
		   permute(subkeys[i],DesPermuted,48) ;
   }
}
		
 //make a local copy of the source text
 memcpy(temp,DesInitial,64);
 //aplit the source text into a left and right block of 32 bits
 memcpy(lblk,temp[0],4);
 memcpy(rblk,&temp[0],4);
 
 //encipher or drcipher the source text 
 for(i=0;i<16;i++){
 	//begin the computation of f 
 	memcpy(fblk,rblk,4);
 	//permute and expand the copy of the right blok into 48 bits .
 	permute(fblk, DesExpansion,48);
 	//apply the oportunities subkey for the round 
 	if(direction==encipher){
 		//for enciphering ,subkeys are applied in increasing order 
 		bit_xor(fblk,subkeys[i],xblk,48);
 		memcpy(fblk,xblk,6);
 		
	 }
	 
	 else{
	 	//for deiphering,subkeys are deplied in decreasing order 
	 	bit_xor(fblk,subkeys[15-i],xblk,48);
	 	memcpy(fblk,xblk,6);
	 	
	 }
//do the s box subtitution

p=0;
for(j=0;j<8;j++){
	//ompute a row and colum into s-box table 
	row=(bit_get(fblk,(j*6)+0)*2)+(bit_get(fblk,(j*6)+5)*1);
	col=(bit_get(fblk,(j*6)+1)*8)+(bit_get(fblk,(j*6)+2)*4)+(bit_get(fblk,(j*6)+4)*1);
	//do the s-box substitution for the current six-bit block.
	sblk=(unsigned char) DesSbox[j] [row] [col];
	for(k=4;k<8;k++){
		bit_set(fblk,p,bit_get(&sblk,k));
		p++;
	}
}

//do the P-box permutation to complete f 
        permute(fblk,DesPbox,32);
        //compute the xor of the left block and f 
        bit_xor(lblk,fblk,xblk,32);
        //set the left block for the round 
        memcpy(lblk,rblk,4);
        //set the right block 
        memcpy(rblk,xblk,4);
    }
    
  //set the target text to the rejoind final right and left blocks
  memcpy(&target[0],rblk,4);
  memcpy(&target,DesFinal,64);
  return 0 ;
}


//des_encipher
void des_encipher(const unsigned char *plaintext,unsigned char *ciphertext,const unsigned char *key){
	
printf("la fonction des encipher en cours . .");
printf("\n");
des_main(plaintext,ciphertext,key,encipher);
   printf("plaintext est : ");
   printf("%s",plaintext );
   printf("\n ");
   printf("ciphertext est : ");
   printf("%s",ciphertext);
   printf("\n ");
   ciph=ciphertext;
   return;   
}
//des_decipher
void des_decipher(const unsigned char *ciphertext,unsigned char *plaintext,const unsigned char *key){
	
printf("la fonction des decipher en cours ...");
printf("\n");
des_main(ciphertext,plaintext,key,decipher);
   printf("ciphertext est : ");
   printf("%s",ciphertext );
   printf("\n ");
   printf("plaintext est : ");
   printf("%s",plaintext);
   printf("\n ");
   
   return;   
  }
   int main(int argc,char *argv[]){
	
	const unsigned char *plaintext= reinterpret_cast <const unsigned char *>("hello");
	const unsigned char *keyt=reinterpret_cast<const unsigned char *>("0E329232EA6D0D73");
	unsigned char Y[8];
	des_encipher(plaintext,Y,key);
	
	  }
